To create a simplified version of the NaturaLens app following the roadmap, here’s a concise implementation plan focused on an MVP (Minimum Viable Product) that captures the core value:

---

**1. Core Features for the Simplified MVP**

- Real-time recognition of a small set of natural objects (e.g., a few mountain peaks and tree species).
- Overlay basic info (object name, simple fact).
- Static AR filters (one or two color grading effects).
- Ability to capture and save photos with overlays.
- No user accounts, social features, or advanced monetization.

---

**2. Technology Stack**

- **Engine:** Unity (2022.3 or newer).
- **AR:** Unity AR Foundation package for cross-platform AR (iOS and Android).
  - [Unity AR Foundation Documentation](https://docs.unity3d.com/Packages/com.unity.xr.arfoundation@latest/)
- **ML Model for Recognition:** MobileNetV2 (pre-trained or with minimal fine-tuning).
- **Backend:** Not required for MVP; all recognition runs on-device.

---

**3. Implementation Steps**

1. **Set Up Unity Project**
   - Install Unity Hub and Unity 2022.3+.
   - Create a new 3D project.
   - Add AR Foundation and ARCore/ARKit packages via Package Manager.

2. **Camera & AR Setup**
   - Configure AR Session and AR Camera in your Scene.
   - Enable plane detection and tracking for basic AR experience.

3. **Object Recognition Integration**
   - Convert or obtain a lightweight MobileNetV2 model in ONNX format.
   - Import Unity Barracuda package for on-device ML inference.
   - Load ONNX model and run inference on camera frames (see [Barracuda Getting Started](https://docs.unity3d.com/Packages/com.unity.barracuda@1.0/manual/GettingStarted.html)).
   - Display recognized object labels as UI overlays.

4. **AR Filters**
   - Use Unity’s Post-Processing (URP/Volume components) to create one or two color grading filters.
   - Allow user to toggle between filters.

5. **Photo Capture**
   - Add a UI button to capture the current AR view (use Unity’s ScreenCapture API).
   - Save images locally on the device.

6. **Basic UI**
   - Simple overlay for object info.
   - Filter toggle button.
   - Capture button.

---

**4. What’s NOT Included in MVP**

- User accounts, profiles, hashtags, likes, or map view.
- Cloud/server-side backend.
- Monetization or subscription features.

---

**5. Next Steps**

- Once MVP is stable, gradually add social features (user accounts, hashtags, map-based feed) and expand recognition database as per the roadmap.

---
Certainly! Here’s a practical, step-by-step guide (with code samples and references) for building a simplified AR app in Unity, covering project setup, ML model inference (object recognition), and AR filter implementation. Each section ends with a clear “bridge” to the next step, so you can follow the workflow smoothly.

---

**1. Unity Project Setup with AR Foundation**

- Install [Unity Hub](https://unity3d.com/get-unity/download).
- Create a new Unity 2022.3 (or newer) 3D project.
- Open the project and install AR Foundation:
  - Go to **Window > Package Manager**.
  - Search for and install **AR Foundation** (latest).
  - Also install **ARCore XR Plugin** (for Android) and/or **ARKit XR Plugin** (for iOS).

- Set up your first AR scene:
  - Create an empty scene, save it as `ARScene`.
  - In the **Hierarchy**, right-click and add:
    - **AR Session**
    - **AR Session Origin** (with an AR Camera inside)
  - Set build target to Android or iOS in **File > Build Settings**.

```csharp
// No code sample needed yet — this is all in the editor!
```
**Bridge to Next Step:**  
Your Unity project can now run a basic AR experience. Next, let’s add real-time ML model inference for object recognition.

---

**2. ML Model Inference in Unity (Object Recognition)**

- **Export/Obtain a MobileNetV2 ONNX Model**  
  You can use a pre-trained [MobileNetV2 ONNX model](https://github.com/onnx/models/tree/main/vision/classification/mobilenet).  
  Download the ONNX file and place it in a `Models` folder in your project.

- **Install Barracuda**  
  In Package Manager, search for and install `Barracuda` (Unity’s ONNX inference library).

- **Basic Inference Script**  
  Create a C# script (e.g., `ObjectRecognizer.cs`) and attach it to an empty GameObject in your scene.

```csharp
using Unity.Barracuda;
using UnityEngine;
using UnityEngine.UI;

public class ObjectRecognizer : MonoBehaviour
{
    public NNModel modelAsset;
    public RawImage cameraFeed; // Assign a RawImage to display camera feed
    private Model runtimeModel;
    private IWorker worker;

    void Start()
    {
        runtimeModel = ModelLoader.Load(modelAsset);
        worker = WorkerFactory.CreateWorker(WorkerFactory.Type.Auto, runtimeModel);
    }

    void Update()
    {
        // 1. Capture camera frame (you need to set up AR camera feed to a Texture2D)
        // 2. Preprocess frame to match model input (size, normalization)
        // 3. Run inference:
        // var input = new Tensor(preprocessedTexture);
        // worker.Execute(input);
        // var output = worker.PeekOutput();
        // string label = InterpretResults(output);
        // Display label as overlay (use UI Text).
    }

    void OnDestroy()
    {
        worker?.Dispose();
    }
}
```
**References:**  
- [Unity Barracuda Getting Started](https://docs.unity3d.com/Packages/com.unity.barracuda@latest/manual/GettingStarted.html)  
- [MobileNetV2 ONNX](https://github.com/onnx/models/tree/main/vision/classification/mobilenet)

**Bridge to Next Step:**  
You can now classify objects in the camera feed. Let’s enhance the visual experience by adding AR filters.

---

**3. AR Filter Implementation in Unity**

- **Enable Universal Render Pipeline (URP)**
  - In Unity, go to **Edit > Project Settings > Graphics** and assign a URP asset.
  - Convert your Scene to URP via **Edit > Render Pipeline > Universal Render Pipeline > Upgrade Project Materials**.

- **Add a Post-Processing Volume**
  - In the Hierarchy, right-click and add an empty GameObject named `PostProcessVolume`.
  - Add the component **Volume**. Create a new **Profile**.
  - Add “Color Adjustments” or “Bloom” overrides.

- **Toggle Filters via UI**
  - Add a **UI Button** to the Canvas.
  - In a script, enable/disable or adjust the filter parameters on button click:

```csharp
using UnityEngine;
using UnityEngine.Rendering;
using UnityEngine.Rendering.Universal;

public class FilterToggle : MonoBehaviour
{
    public Volume postProcessVolume;
    private ColorAdjustments colorAdjustments;

    void Start()
    {
        postProcessVolume.profile.TryGet(out colorAdjustments);
    }

    public void ToggleFilter()
    {
        colorAdjustments.saturation.value = colorAdjustments.saturation.value == 0 ? -50 : 0;
        // Add more effects as needed
    }
}
```
**References:**  
- [Unity URP Post-processing Overview](https://docs.unity3d.com/Packages/com.unity.render-pipelines.universal@latest/manual/post-processing.html)
- [Unity Learn: Post-processing](https://learn.unity.com/tutorial/creating-a-global-post-processing-volume-2019-3)

**Bridge to Next Step:**  
You now have object recognition with AR overlays and toggleable filters. Next, add a button to capture AR photos, or expand to social features per the roadmap.

---
Certainly! Here’s how you can add photo capture functionality and polish the UI in your Unity-based AR app, step by step:

---

**1. Photo Capture in Unity (AR View)**

a. **Add a UI Button for Capture**
- In your Unity scene, open the Canvas.
- Add a Button (right-click Canvas > UI > Button).
- Name it `CaptureButton` and position it for easy access.

b. **Script to Capture the Screen**
- Create a C# script (e.g., `PhotoCapture.cs`) and attach it to an empty GameObject.
- Reference the Button in the script via the Inspector.

```csharp
using UnityEngine;
using UnityEngine.UI;
using System.IO;

public class PhotoCapture : MonoBehaviour
{
    public Button captureButton;

    void Start()
    {
        captureButton.onClick.AddListener(CaptureScreenshot);
    }

    void CaptureScreenshot()
    {
        string filename = $"NLens_{System.DateTime.Now:yyyyMMdd_HHmmss}.png";
        string path = Path.Combine(Application.persistentDataPath, filename);
        ScreenCapture.CaptureScreenshot(filename);
        Debug.Log($"Photo saved to: {path}");
        // Optionally, show a toast or popup that photo was saved
    }
}
```
- Assign the `CaptureButton` in the Inspector.

**Tips:**
- On Android/iOS, screenshots save to the app’s persistent data path. For user access, you may want to add gallery integration later.

---

**2. UI Polish**

a. **Improve Button Appearance**
- Change the Button’s background and text to make it more visible (use a camera icon).
- Set Button colors for normal, pressed, and highlighted states.

b. **Add Feedback for Capture**
- Add a UI Text or Image (“Photo Saved!”) that appears briefly after a capture.
- Use a coroutine to show/hide this feedback.

```csharp
public GameObject feedbackPanel;

void CaptureScreenshot()
{
    // ... (capture code as above)
    StartCoroutine(ShowFeedback());
}

IEnumerator ShowFeedback()
{
    feedbackPanel.SetActive(true);
    yield return new WaitForSeconds(1.5f);
    feedbackPanel.SetActive(false);
}
```

c. **Organize UI Elements**
- Keep overlays (labels, filter toggles) unobtrusive and semi-transparent.
- Place the capture button at the bottom center or a corner for ergonomic access.
- Make sure UI scales well on different screen sizes (use Canvas Scaler).

d. **Optional: Add Animations**
- Use simple scale or fade-in animations when showing overlays or feedback for a more polished feel.
- Example: Animate the capture button when pressed.

---

**Bridge to Next Step**

With photo capture and an improved UI, your app will feel much more user-friendly and engaging. Next, you can add a gallery view to browse saved photos, or begin implementing social features like sharing or hashtagging. Let me know if you want to proceed with those next steps!

Great! Here’s how to proceed after implementing photo capture and UI polish in your Unity AR app:

---

**1. (Optional) Add Gallery Viewing for Captured Photos**

- Since screenshots are saved to Application.persistentDataPath, you can add a simple gallery screen:
  - Create a new UI panel for the gallery.
  - Use System.IO to list all files with the NLens_*.png pattern.
  - For each file, create an Image UI element and load the texture:
    ```csharp
    byte[] imgData = File.ReadAllBytes(filePath);
    Texture2D tex = new Texture2D(2, 2);
    tex.LoadImage(imgData);
    // Assign tex to a RawImage component
    ```
  - Provide a button to switch between AR view and gallery.

---

**2. (Optional) Share or Export Photos**

- Add a “Share” button for each gallery image.
- On Android/iOS, use a plugin (like Native Share) to open the platform’s share dialog:
  - [Native Share for Unity (GitHub)](https://github.com/yasirkula/UnityNativeShare)
- For web or desktop, offer a “Save As” dialog or copy to clipboard.

---

**3. Next Roadmap Steps (Social Features)**

- If you want to expand per the roadmap, here are possible next features:
  1. **User Accounts:** Integrate a simple authentication (Firebase Auth or PlayFab).
  2. **Hashtags/Annotations:** Let users tag photos with hashtags and display them in the gallery.
  3. **Likes/Trending:** Enable a simple like system for saved photos (store locally or sync to a backend).
  4. **Map-Based Feed:** Use a map UI (like Mapbox SDK or Unity’s map assets) to show where photos were taken.

---

Here’s a step-by-step guide to add a basic map-based feed to your Unity AR app. This feature will let users view the locations where photos were taken, with pins or markers on a map.

---

**1. Choose a Map Solution for Unity**

- Recommended: [Mapbox Unity SDK](https://docs.mapbox.com/unity/maps/overview/) (free for limited use, good documentation, supports mobile).
- Alternatives: [Online Maps](https://assetstore.unity.com/packages/tools/integration/online-maps-14347), [Google Maps SDK for Unity (third-party wrappers only)].

---

**2. Set Up Mapbox in Unity**

a. Register at [Mapbox](https://account.mapbox.com/) and get an API access token.

b. In Unity:
- Open Package Manager and import Mapbox Unity SDK, or download and import the Unity package from Mapbox’s website.
- Drag the Mapbox prefab “AbstractMap” into your scene.

c. Configure AbstractMap:
- Set map type (satellite, streets).
- Enter your Mapbox API token in the Mapbox configuration.

---

**3. Store Photo Location Data**

- When capturing a photo, use Unity’s location service to store GPS coordinates alongside the photo’s filename.

```csharp
[System.Serializable]
public class PhotoData
{
    public string filename;
    public double latitude;
    public double longitude;
}
```

- Save a list of PhotoData (as JSON or PlayerPrefs). Example when capturing:

```csharp
IEnumerator CaptureWithLocation()
{
    if (!Input.location.isEnabledByUser)
        yield break;
    Input.location.Start();
    yield return new WaitForSeconds(1);
    double lat = Input.location.lastData.latitude;
    double lon = Input.location.lastData.longitude;
    // Save lat/lon with photo filename
}
```

---

**4. Place Pins/Markers for Photos**

- On the map, iterate through saved PhotoData.
- For each, use Mapbox’s `SpawnOnMap` example or equivalent to instantiate a marker prefab at the GPS coordinates.

Example:

```csharp
public GameObject markerPrefab;
public AbstractMap map; // Reference to Mapbox AbstractMap

void PlacePhotoMarker(PhotoData data)
{
    Vector2d location = new Vector2d(data.latitude, data.longitude);
    var marker = Instantiate(markerPrefab);
    marker.transform.localPosition = map.GeoToWorldPosition(location, true);
    // Optionally, attach a click event to open the photo
}
```

---

**5. UI for Map-Based Feed**

- Add a button to switch between AR/camera view and map feed.
- Clicking a marker shows a popup with the photo and info (load texture from path).

---

**6. Bridge to Advanced Features**

- Add clustering for overlapping markers.
- Let users search or filter by hashtags, time, etc.
- Sync photo/location data to backend for social/multi-user feed.

---

**References:**
- [Mapbox Unity Maps SDK Docs](https://docs.mapbox.com/unity/maps/overview/)
- [Unity Location Service](https://docs.unity3d.com/ScriptReference/LocationService.html)
- [Spawn Pins Example (Mapbox)](https://docs.mapbox.com/unity/maps/examples/spawn-on-map/)

Would you like code samples for a specific step, like saving/loading photo locations or handling marker clicks?

Here’s a detailed, actionable step-by-step for both saving photo location data and placing map pins (markers) in your Unity AR app, enabling a map-based feed:

---

**1. Save Photo Location Data When Capturing**

a. **Enable Location Services**

```csharp
IEnumerator StartLocationService()
{
    if (!Input.location.isEnabledByUser)
    {
        Debug.Log("Location services not enabled.");
        yield break;
    }
    Input.location.Start();
    int maxWait = 20;
    while (Input.location.status == LocationServiceStatus.Initializing && maxWait > 0)
    {
        yield return new WaitForSeconds(1);
        maxWait--;
    }
    if (Input.location.status != LocationServiceStatus.Running)
    {
        Debug.Log("Unable to start location services.");
        yield break;
    }
    // Location services are ready
}
```

b. **Capture and Save Location with Photo**

```csharp
[System.Serializable]
public class PhotoData
{
    public string filename;
    public double latitude;
    public double longitude;
}

public List<PhotoData> allPhotos = new List<PhotoData>();

void CaptureAndSavePhoto()
{
    StartCoroutine(CaptureWithLocation());
}

IEnumerator CaptureWithLocation()
{
    yield return StartLocationService();

    string filename = $"NLens_{System.DateTime.Now:yyyyMMdd_HHmmss}.png";
    string path = System.IO.Path.Combine(Application.persistentDataPath, filename);
    ScreenCapture.CaptureScreenshot(filename);

    double lat = Input.location.lastData.latitude;
    double lon = Input.location.lastData.longitude;

    PhotoData pd = new PhotoData { filename = filename, latitude = lat, longitude = lon };
    allPhotos.Add(pd);

    // Persist the list using JSON
    string json = JsonUtility.ToJson(new Serialization<PhotoData>(allPhotos));
    PlayerPrefs.SetString("PhotoDataList", json);
    PlayerPrefs.Save();
}

// Helper for serializing lists
[System.Serializable]
public class Serialization<T>
{
    public List<T> target;
    public Serialization(List<T> target) { this.target = target; }
}
```

c. **Load Photo Data on App Start**

```csharp
void LoadPhotoData()
{
    if (PlayerPrefs.HasKey("PhotoDataList"))
    {
        string json = PlayerPrefs.GetString("PhotoDataList");
        allPhotos = JsonUtility.FromJson<Serialization<PhotoData>>(json).target;
    }
}
```

---

**2. Place Pins/Markers on the Map (Mapbox Unity SDK Example)**

a. **Setup Mapbox**
- Add the AbstractMap prefab to your scene.
- Assign your Mapbox token.

b. **Create a Marker Prefab**
- Make a simple prefab (e.g., a small sphere or icon) to represent a photo location.

c. **Place Markers for Each Photo**

```csharp
using Mapbox.Utils;
using Mapbox.Unity.Map;

public GameObject markerPrefab;
public AbstractMap map; // Assign in Inspector

void PlaceAllPhotoMarkers()
{
    foreach (var pd in allPhotos)
    {
        PlacePhotoMarker(pd);
    }
}

void PlacePhotoMarker(PhotoData data)
{
    Vector2d location = new Vector2d(data.latitude, data.longitude);
    var marker = Instantiate(markerPrefab, map.GeoToWorldPosition(location, true), Quaternion.identity, map.transform);

    // Optionally, attach the filename or photo to the marker for display on click
    marker.GetComponent<YourMarkerScript>().photoData = data;
}
```

d. **Handle Marker Clicks to Show Photo**

```csharp
public class YourMarkerScript : MonoBehaviour
{
    public PhotoData photoData;

    void OnMouseDown()
    {
        // Load and display the photo (e.g., show in a UI popup)
        string path = System.IO.Path.Combine(Application.persistentDataPath, photoData.filename);
        byte[] imgData = System.IO.File.ReadAllBytes(path);
        Texture2D tex = new Texture2D(2, 2);
        tex.LoadImage(imgData);
        // Assign tex to a RawImage UI element
    }
}
```

---

**Bridge to Advanced Social Features**
- You can now display a map-based feed of where photos were taken!
- Next, add features such as hashtag filtering, syncing to a backend, or displaying trending locations.

- Here’s a comprehensive step-by-step guide covering all three parts for your Unity AR app’s map-based feed:

---

### 1. Saving Photo Location Data

**a. Enable Location Services**
```csharp
IEnumerator StartLocationService()
{
    if (!Input.location.isEnabledByUser)
        yield break;
    Input.location.Start();
    int maxWait = 20;
    while (Input.location.status == LocationServiceStatus.Initializing && maxWait > 0)
    {
        yield return new WaitForSeconds(1);
        maxWait--;
    }
    if (Input.location.status != LocationServiceStatus.Running)
        yield break;
}
```

**b. Capture Photo and Store GPS**
```csharp
[System.Serializable]
public class PhotoData
{
    public string filename;
    public double latitude;
    public double longitude;
}

public List<PhotoData> allPhotos = new List<PhotoData>();

void CaptureAndSavePhoto()
{
    StartCoroutine(CaptureWithLocation());
}

IEnumerator CaptureWithLocation()
{
    yield return StartLocationService();

    string filename = $"NLens_{System.DateTime.Now:yyyyMMdd_HHmmss}.png";
    ScreenCapture.CaptureScreenshot(filename);

    double lat = Input.location.lastData.latitude;
    double lon = Input.location.lastData.longitude;

    PhotoData pd = new PhotoData { filename = filename, latitude = lat, longitude = lon };
    allPhotos.Add(pd);

    // Persist list as JSON
    string json = JsonUtility.ToJson(new Serialization<PhotoData>(allPhotos));
    PlayerPrefs.SetString("PhotoDataList", json);
    PlayerPrefs.Save();
}

[System.Serializable]
public class Serialization<T>
{
    public List<T> target;
    public Serialization(List<T> target) { this.target = target; }
}
```

**c. Load Photo Data**
```csharp
void LoadPhotoData()
{
    if (PlayerPrefs.HasKey("PhotoDataList"))
        allPhotos = JsonUtility.FromJson<Serialization<PhotoData>>(PlayerPrefs.GetString("PhotoDataList")).target;
}
```

---

### 2. Placing Pins/Markers on Map (Mapbox)

**a. Setup**
- Add AbstractMap prefab and assign your Mapbox token.
- Create a marker prefab (e.g., a sphere or image).

**b. Place Markers**
```csharp
using Mapbox.Utils;
using Mapbox.Unity.Map;

public GameObject markerPrefab;
public AbstractMap map; // Assign in Inspector

void PlaceAllPhotoMarkers()
{
    foreach (var pd in allPhotos)
        PlacePhotoMarker(pd);
}

void PlacePhotoMarker(PhotoData data)
{
    Vector2d location = new Vector2d(data.latitude, data.longitude);
    var marker = Instantiate(markerPrefab, map.GeoToWorldPosition(location, true), Quaternion.identity, map.transform);

    // Attach data for click handling
    marker.GetComponent<YourMarkerScript>().photoData = data;
}
```

---

### 3. Handling Marker Clicks to Show Photo

**a. Marker Script**
```csharp
public class YourMarkerScript : MonoBehaviour
{
    public PhotoData photoData;

    void OnMouseDown()
    {
        string path = System.IO.Path.Combine(Application.persistentDataPath, photoData.filename);
        byte[] imgData = System.IO.File.ReadAllBytes(path);
        Texture2D tex = new Texture2D(2, 2);
        tex.LoadImage(imgData);
        // Display tex in a RawImage UI element, e.g., assign to a popup panel
        FindObjectOfType<FeedUIManager>().ShowPhoto(tex, photoData);
    }
}
```

**b. UI Photo Popup Example**
```csharp
public class FeedUIManager : MonoBehaviour
{
    public GameObject popupPanel;
    public UnityEngine.UI.RawImage photoDisplay;
    public UnityEngine.UI.Text locationText;

    public void ShowPhoto(Texture2D tex, PhotoData pd)
    {
        popupPanel.SetActive(true);
        photoDisplay.texture = tex;
        locationText.text = $"Lat: {pd.latitude:F5}, Lon: {pd.longitude:F5}";
    }

    public void HidePhoto()
    {
        popupPanel.SetActive(false);
    }
}
```
---

**Result:**  
- Each photo is saved with its GPS location.
- Markers appear on the map at capture locations.
- Tapping/clicking a marker shows the associated photo and location info.

Let me know if you want to extend this with hashtag filtering, photo sharing, or backend sync!

