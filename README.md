# Back Home: A Computer Vision Solution to Seashell Identification for Ecological Restoration (Seashell Animation)

An Augmented Reality web application that displays a composite video overlay when a specific seashell target image is detected using your device's camera.

**Note: This application is part of the research paper presented at the International Conference on Computer Vision, 2025. (CV4E)**

## Overview

This AR application uses image tracking to display a transparent video overlay in 3D space. When the camera detects the target seashell image, it plays two synchronized videos - one containing the color information and another containing the alpha (transparency) channel - creating a realistic composited AR experience.

## Features

- **Image-based AR tracking** using MindAR
- **Transparent video compositing** with custom shader
- **Automatic video playback control** based on target detection
- **Mobile-friendly** with touch controls
- **No app installation required** - runs directly in web browsers

## Technical Stack

- **A-Frame 1.6.0** - Web framework for building 3D/VR experiences
- **MindAR 1.2.5** - Computer vision library for AR image tracking
- **Custom GLSL Shader** - For real-time video alpha compositing
- **WebRTC** - For camera access and video processing

## Dependencies

The application automatically loads these external libraries:

```html
<script src="https://aframe.io/releases/1.6.0/aframe.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/mind-ar@1.2.5/dist/mindar-image-aframe.prod.js"></script>
```

## Setup and Usage

### 1. Host the Files
- Upload the `index.html` file to a web server
- Ensure HTTPS hosting (required for camera access)

### 2. Access the Application
- Open the hosted URL in a mobile browser
- Grant camera permissions when prompted
- Point your camera at the seashell target image

### 3. Target Image
The application tracks the seashell image defined in:
```
https://raw.githack.com/alevalve/backhome_ar_seashell/main/target.mind
```

## Video Assets

The application uses two synchronized video files:

- **Color Video**: `AR_Video01_Color.mp4` - Contains the RGB color information
- **Alpha Video**: `AR_Video01_Alpha.mp4` - Contains the transparency mask (black = transparent, white = opaque)

Both videos are hosted at:
```
https://raw.githack.com/alevalve/backhome_ar_seashell/main/
```

## How It Works

### 1. Image Tracking
- MindAR processes camera feed to detect the seashell target image
- When target is found, triggers `targetFound` event
- When target is lost, triggers `targetLost` event

### 2. Video Compositing
The custom `video-matte` shader combines two video sources:

```glsl
// Samples color from RGB video
vec4 c = texture2D(videoColor, vUV);
// Samples alpha from grayscale video
float a = texture2D(videoAlpha, vUV).r;
// Applies smoothstep for cleaner edges
a = smoothstep(0.15, 0.95, a) * opacity;
// Outputs composited result
gl_FragColor = vec4(c.rgb, a);
```

### 3. Playback Control
```javascript
// When target detected
marker.addEventListener('targetFound', async () => {
    plane.setAttribute('visible', true);
    vc.muted = false;
    try { await vc.play(); } catch {}
    try { await va.play(); } catch {}
});

// When target lost
marker.addEventListener('targetLost', () => {
    plane.setAttribute('visible', false);
    vc.pause(); va.pause();
    vc.muted = true;
    vc.currentTime = 0; va.currentTime = 0;
});
```

## Browser Compatibility

### Supported Browsers
- Chrome (Android/iOS)
- Safari (iOS)
- Firefox (Android)
- Edge (mobile)

### Requirements
- HTTPS connection
- Camera access permissions
- WebGL support
- Modern ES6+ JavaScript support

## Customization

### Change Target Image
1. Create a new `.mind` file using MindAR tools
2. Replace the `imageTargetSrc` URL:
```html
mindar-image="imageTargetSrc: YOUR_TARGET_URL_HERE"
```

### Update Videos
Replace the video URLs in the `<a-assets>` section:
```html
<video id="videoColor" src="YOUR_COLOR_VIDEO_URL"></video>
<video id="videoAlpha" src="YOUR_ALPHA_VIDEO_URL"></video>
```

### Adjust Video Dimensions
Modify the plane size in the `<a-plane>` element:
```html
<a-plane width="0.9" height="0.55" ...>
```

### Shader Parameters
Adjust the alpha smoothstep values for different transparency effects:
```glsl
a = smoothstep(0.15, 0.95, a) * opacity;
```
- Lower first value (0.15) = more transparent areas
- Higher second value (0.95) = sharper edges

## Project Structure

```
backhome_ar_seashell/
├── index.html              # Main AR application
├── target.mind             # MindAR target image data
├── AR_Video01_Color.mp4    # Color video track
└── AR_Video01_Alpha.mp4    # Alpha video track
```

## Troubleshooting

### Video Won't Play
- Ensure videos are in supported formats (MP4 with H.264)
- Check CORS headers if videos are hosted externally
- Verify HTTPS hosting for both page and video assets
- Check network connectivity to GitHub raw content

### AR Tracking Issues
- Ensure good lighting conditions
- Seashell target should be clearly visible and unobstructed
- Hold device steady and at appropriate distance (30cm-1m)
- Check camera permissions are granted
- Avoid reflective surfaces that might interfere with tracking
- For iOS systems is required touch the screen to activate the sound

## License

This project uses open-source libraries:
- A-Frame (MIT License)
- MindAR (MIT License)

Check individual license requirements for commercial use.

## Authors

- Vivian Mayorga (3D Artist)
- Alexander Valverde (Developer)
- André Montoya (Developer)
