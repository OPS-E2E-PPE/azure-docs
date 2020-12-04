---
title: SDK Overview
description: Get familiar with the Object Anchors SDK.
author: craigktreasure
manager: vriveras
services: azure-object-anchors

ms.author: crtreasu
ms.date: 04/01/2020
ms.topic: conceptual
ms.service: azure-object-anchors
---

# API Overview

This section provides a high-level overview of the **Object Anchors** constructs. You'll gain an understanding of how an object is represented and what the various components are used for.

All of the types described below can be found in the **Microsoft.MixedReality.ObjectUnderstanding** namespace.

## Types

### ObjectModel

An object model represents a physical object's geometry and encodes necessary parameters for detection and pose estimation. It must be created using the [Object Anchors Ingestion Service](../quickstarts/get-started-model-ingestion.md). Then an application can load the generated model file using **Object Anchors** API and query the mesh embedded in that model for visualization.

### ObjectSearchArea

An object search area specifies the space to look for one or multiple objects. It's defined by a spatial graph node ID and a spatial bounds in the coordinate system represented by the spatial graph node ID. The **Object Anchors** SDK supports four types of bounds, namely, **field of view**, **bounding box**, **sphere**, and a **location**.

### ObjectQuery

An object query tells an **object observer** how to find objects of a given model. It provides the following tunable parameters, whose default values can be retrieved from an object model.

#### MinSurfaceCoverage

The minimum required surface coverage to consider an instance is detected.

For each object candidate, an **observer** computes the ratio of overlapped surfaces between transformed object model and the scene, then it reports that candidate to application only when the coverage ratio is above a given threshold.

#### IsExpectedToBeStandingOnGroundPlane

If the target object is expected to stand on the ground plane.

A ground plane is the lowest horizontal floor in the search area. It provides good constraint on the possible object poses. Turning on this flag will guide the **observer** to estimate the pose in a limited space and could improve the accuracy. This parameter will be ignored if the model is not supposed to stand on the ground plane.

#### ExpectedMaxVerticalOrientationInDegrees

Expected maximum angle in degrees between up direction of an object instance and gravity.

This parameter provides additional constraint on the up direction of an estimated pose. For example, if an object is up-right, this parameter can be 0. **Object Anchors** is not supposed to detect objects that are different from the model. If a model is up-right, then it won't detect an instance laid side-down. A new model would be used for side-down layout. Same rule applies for articulation.

#### SearchAreas

An array of spatial bounds where to find object(s).

The **observer** will look for objects in the union space of all search areas specified in a query. In this release, we will return at most one object with highest confidence to reduce the latency.

### ObjectInstance

An object instance represents a hypothetical position where an instance of a given model could be in the HoloLens coordinate system. Each instance comes with a `SurfaceCoverage` property to indicate how good the estimated pose is.

An instance is created by calling `ObjectObserver.DetectAsync` method, then updated automatically in the background when alive. An application can listen to the state changed event on a particular instance or change the tracking mode to pause/resume the update. An instance will automatically be removed from the **observer** when tracking is lost.

### ObjectObserver

An **object observer** loads object models, detects their instances, and reports 6-DoF poses of each instance in HoloLens coordinate system.

Although any object model or instance is created from an **observer**, their lifetimes are independent, meaning an application can dispose an observer and continue to use the object model or instance.

### ObjectDiagnosticsSession

The object diagnostics session records diagnostics and writes data to an archive.

A diagnostics archive includes the scene point cloud, observer's status, and information about the models. This information is useful to identify possible runtime issues. For more information, see the [FAQ](../faq.md).

## SDK usage and details

This section should provide you with the basics of how to use the SDK, at which point you should have enough context to browse through the sample applications to see how **Object Anchors** is used holistically.

### Initialization

Applications need to call the `ObjectObserver.IsSupported()` API to determine if **Object Anchors** is supported on the device before using it. If the `ObjectObserver.IsSupported()` API returns `false`, check that the application has enabled the **spatialPerception** capability and\or upgrade to the latest HoloLens OS.

```cs
if (!ObjectObserver.IsSupported())
{
    // Handle the error
}

// This call should grant the access we need.
var status = await ObjectObserver.RequestAccessAsync();
if(status != ObjectObserverStatus.Allowed)
{
    // Handle the error
}
```

Next, the application creates an object observer and loads necessary models generated by [Object Anchors Ingestion Service](../quickstarts/get-started-model-ingestion.md).

```cs
var observer = new ObjectObserver();

byte[] modelAsBytes; // Load a model into a byte array. Model could be a file, an embedded resource, or a network stream.
var model = await observer.LoadObjectModelAsync(modelAsBytes);
```

The application creates a query to detect instances of that model within a space.

```cs
var coordinateSystem = default(SpatialGraphCoordinateSystem);
var searchAreaAsBox = ObjectSearchArea.FromOrientedBox(coordinateSystem, default(SpatialOrientedBox));

// Optionally change the parameters, otherwise use the default values embedded in the model.
var query = new ObjectQuery(model);
query.MinSurfaceCoverage = 0.2f;
query.ExpectedMaxVerticalOrientationInDegrees = 1.5f;
query.SearchAreas.Add(searchAreaAsBox);

// Detection could take long, run in a background thread.
var detectedObjects = await observer.DetectAsync(query);
```

By default, each detected instance will be tracked automatically by the **observer**. We can optionally manipulate these instances by changing their tracking mode or listening to their state changed event.

```cs
foreach (var instance in detectedObjects)
{
    // Supported modes:
    // "LowLatencyCoarsePosition" - consumes less CPU cycles thus fast to update the state.
    // "HighLatencyAccuratePosition" - (not yet implemented) consumes more CPU cycles thus potentially taking longer time to update the state.
    // "Paused" - stops to update the state until mode changed to low or high.
    instance.Mode = ObjectInstanceTrackingMode.LowLatencyCoarsePosition;

    // Listen to state changed event on this instance.
    instance.Changed += InstanceChangedHandler;

    // Optionally dispose an instance if not interested in it.
    //instance.Dispose();
}
```

In the state changed event, we can query the latest state or dispose an instance if it lost tracking.

```cs
var InstanceChangedHandler = new Windows.Foundation.TypedEventHandler<ObjectInstance, ObjectInstanceChangedEventArgs>((sender, args) =>
{
    // Try to query the current instance state.
    var state = sender.TryGetCurrentState();

    if (state.HasValue)
    {
        // Process latest state via state.Value.
    }
    else
    {
        // This object instance is lost for tracking, and will never be recovered.
        // The caller can detatch the Changed event handler from this instance and dispose it.
        sender.Dispose();
    }
});
```

Also, an application can optionally record one or multiple diagnostics sessions for offline debugging.

```cs
string diagnosticsFolderPath = Windows.Storage.ApplicationData.Current.TemporaryFolder.Path;
const uint maxSessionSizeInMegaBytes = uint.MaxValue;

// Recording starts on the creation of a diagnostics session.
var diagnostics = new ObjectDiagnosticsSession(observer, maxSessionSizeInMegaBytes);

// Wait for **observer** do some job.

// Application can report some **pseudo ground-truth** pose for an instance acquired from other means.
diagnostics.ReportActualInstanceLocation(instance, coordinateSystem, Vector3.Zero, Quaternion.Identity);

// Close a session and write the diagnostics into an archive at specified location.
await diagnostics.CloseAsync(System.IO.Path.Combine(diagnosticsFolderPath, "diagnostics.zip"));
```

Finally we release resources by disposing all objects.

```cs
foreach(var instance in activeInstances)
{
    instance.Changed -= InstanceChangedHandler;
    instance.Dispose();
}

model.Dispose();
observer.Dispose();
```
