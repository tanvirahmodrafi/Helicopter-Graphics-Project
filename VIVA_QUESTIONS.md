# Three.js Helicopter Helipad Project Viva Notes

Use these answers as a guide, then explain them in your own words.

## Quick Project Summary

This project is a Three.js scene where a realistic NASA Ingenuity Mars Helicopter GLB model is placed on a daylight helipad. A large helipad terminal/control building, access road, trees, textured ground, landing lights, rotor blur, clouds, and a bright sky make the scene more realistic. The mouse controls a point light source, `A/D/W/S/Q/E` control the camera, arrow keys move the helicopter forward/back/left/right, `O/P` lift and lower it, `0` starts helicopter wing rotation, `9` stops it, and the mouse wheel controls the light height.

## Important Files

- `texture.html`: Main project file. It creates the scene, camera, lights, helipad, model loader, controls, and render function.
- `assets/models/ingenuity-mars-helicopter.glb`: The realistic helicopter model used in the scene.
- `assets/models/README.md`: Credit/source information for the model.
- `js/three.js`: Main Three.js library.
- `js/GLTFLoader.js`: Loads `.gltf` and `.glb` 3D model files.
- `js/DRACOLoader.js`: Helps load Draco-compressed GLB models.
- `js/libs/draco/gltf/draco_wasm_wrapper.js`: Draco decoder wrapper.
- `js/libs/draco/gltf/draco_decoder.wasm`: Draco decoder binary.

## Common Viva Questions And Easy Answers

### 1. What is this project about?

It is a 3D helicopter-on-helipad scene made with Three.js. I load a realistic NASA helicopter model, place it on a helipad, add a terminal building, trees, landing lights, and a daylight sky, and control the light with the mouse.

### 2. Which file is the main file?

The main file is `texture.html`. Almost all my project logic is inside this file.

### 3. How did you get/download the helicopter model?

I used the NASA Ingenuity Mars Helicopter GLB model from the public NASA 3D Resources repository. The model source is mentioned in `assets/models/README.md`. I saved the model inside my project as `assets/models/ingenuity-mars-helicopter.glb`.

### 4. Which helicopter model file did you add?

I added:

```text
assets/models/ingenuity-mars-helicopter.glb
```

This is the 3D helicopter model file.

### 5. What is a `.glb` file?

`.glb` is a binary glTF file. It can contain 3D geometry, materials, textures, and scene information in one file. It is commonly used for 3D models on the web.

### 6. How do you show the helicopter on the helipad?

In `texture.html`, I use `THREE.GLTFLoader()` to load `assets/models/ingenuity-mars-helicopter.glb`. After loading, I get the model from `gltf.scene`, scale it with `fitImportedHelicopterToHelipad()`, prepare the materials, collect the rotor objects, and add it to `importedHelicopterGroup`.

Key code area:

```js
helicopterLoader.load("assets/models/ingenuity-mars-helicopter.glb", function (gltf) {
    const importedHelicopter = gltf.scene;
    fitImportedHelicopterToHelipad(importedHelicopter);
    importedHelicopterGroup.add(importedHelicopter);
});
```

### 7. Why do you use `GLTFLoader.js`?

Three.js by itself does not directly load GLB files with the core library. `GLTFLoader.js` is an extra Three.js loader that understands `.gltf` and `.glb` models.

### 8. Why do you use `DRACOLoader.js`?

The helicopter model is Draco-compressed, which means the mesh data is compressed to reduce file size. `DRACOLoader.js` decodes that compressed geometry so Three.js can display the model.

### 9. Which Draco files are required?

These two decoder files are used:

```text
js/libs/draco/gltf/draco_wasm_wrapper.js
js/libs/draco/gltf/draco_decoder.wasm
```

The loader path is set here:

```js
dracoLoader.setDecoderPath("js/libs/draco/gltf/");
```

### 10. Where is the helipad code?

The helipad code is in `texture.html`:

- `createHelipadTexture()` creates concrete noise, safety rings, and a large `H`.
- `createAccessRoadTexture()` creates the asphalt service road texture.
- The `helipadMaterial` uses that texture with a custom shader.
- `CircleGeometry` creates the circular landing pad.
- `createAccessRoad()` connects the terminal building to the helipad.
- `createHelipadLights()` places small colored landing lights around the pad.
- `createHelipadScenery()` places the big terminal building and trees around the pad.

### 11. How is the helipad made?

The helipad is made from a flat circle geometry:

```js
new THREE.CircleGeometry(10, 96)
```

It is rotated flat using:

```js
helipad.rotation.x = -Math.PI / 2;
```

### 12. How did you replace the road?

I removed the multiple road planes and road segment code. Then I added one circular helipad mesh, changed the road texture function into `createHelipadTexture()`, and arranged the scenery around the landing pad instead of along a road.

### 13. Is the helicopter actually moving?

The helicopter starts near the center of the helipad. Arrow keys move it forward/back/left/right, and `O/P` lift or lower it. When the user presses `0`, the helicopter wing/rotor objects start rotating and rotor blur appears. When the user presses `9`, rotation and blur stop.

### 14. Why did you stop the unlimited loop?

The previous version used `requestAnimationFrame()` continuously. I replaced it with `renderScene()` for normal updates, so the scene renders when something changes. A small rotor animation loop starts only after the user presses `0`, and it stops when the user presses `9`.

### 15. How does the mouse control the light?

Mouse position is 2D screen data, but the scene is 3D. I use `THREE.Raycaster()` to cast a ray from the camera through the mouse position. The ray hits the landing plane, and the point light moves above that hit point.

### 16. Why do you use `Raycaster`?

Raycaster converts mouse screen position into a 3D position in the scene. Without it, the mouse only gives X/Y screen pixels, not a real 3D world position.

### 17. What does the mouse wheel do?

The mouse wheel changes the height of the point light. The height is clamped between `minHeight` and `maxHeight` so it does not go too low or too high.

### 18. What lights are used?

I use three lights:

- `AmbientLight`: soft overall light.
- `PointLight`: main light controlled by the mouse.
- `HemisphereLight`: extra sky/ground fill light.

### 19. Where is the camera code?

The camera is created with:

```js
new THREE.PerspectiveCamera(55, window.innerWidth / window.innerHeight, 0.1, 500)
```

The `updateCamera()` function places the camera around the helicopter using `Math.cos()` and `Math.sin()`.

### 20. How does keyboard control work?

The program stores pressed keys in `keyState`. When a control key is pressed, `handleKeyboard(delta)` updates the camera or helicopter position and then redraws the scene.

Controls:

- `A/D`: rotate camera around helicopter.
- `W/S`: change camera height.
- `Q/E`: zoom out/in.
- `Left`: move the helicopter left.
- `Right`: move the helicopter right.
- `Up`: move the helicopter forward.
- `Down`: move the helicopter backward.
- `O`: lift the helicopter.
- `P`: lower the helicopter to the ground.
- `0`: start the helicopter wing/rotor rotation.
- `9`: stop the helicopter wing/rotor rotation.

### 21. What replaced the animation loop?

The project now uses a `renderScene()` function for normal scene updates instead of starting a continuous animation loop immediately. Event handlers call it after keyboard, mouse, wheel, resize, or model-load changes. When the user presses `0`, a small rotor animation loop starts and repeatedly renders only for the helicopter wing/rotor movement. Pressing `9` cancels that loop.

### 22. How did you make the scene more realistic?

I added several realistic details:

- Small red, green, and white helipad boundary lights.
- A generated grass/dirt ground texture instead of a flat green plane.
- An asphalt access road and walkway between the terminal and helipad.
- Transparent rotor blur disks that appear when the rotor starts.
- One large terminal/control building near the helipad.

### 23. Why is there a custom shader?

The shader is used mainly for the helipad material. It lets the helipad texture react to the mouse-controlled point light with diffuse and specular lighting.

### 24. What is `uLightPos`?

`uLightPos` is a shader uniform. It sends the point light position from JavaScript to the shader so the helipad can be shaded based on the light position.

### 25. Why do you need a local server?

Browsers often block loading local model files directly from `file://`. A local server like Live Server serves the files with `http://127.0.0.1:5500/`, so `GLTFLoader` can load the GLB and decoder files correctly.

### 26. What old/extra code was removed?

The old Ferrari car code, road segment code, small scattered building layout, and wheel animation logic were removed. The project now uses the NASA Ingenuity Mars Helicopter GLB model on a lit helipad with one larger terminal building.

### 27. What part did AI help with?

AI helped me modify and organize the code, but I understand the important parts: model loading, helipad creation, mouse light control, camera control, rotor animation, and rotor blur.

### 28. Where is the building and tree code?

The building and tree code is in `texture.html` near the helipad creation code. The important functions are:

- `createBuilding()`: creates a building from box geometry and adds small window boxes.
- `createHelipadTerminal()`: creates the large terminal/control building.
- `createTree()`: creates a tree using a cylinder trunk and cone-shaped leaves.
- `createHelipadScenery()`: places buildings and trees around the helipad.

### 29. Did you import the buildings and trees?

No. I made them using basic Three.js geometry. Buildings use `BoxGeometry`, tree trunks use `CylinderGeometry`, and tree tops use `ConeGeometry`. This keeps the project simple and easier to explain.

### 30. How did you make the sky realistic?

I made the sky procedurally in `texture.html`. The function `createSkyTexture()` creates a bright blue daylight gradient and horizon glow. Then I put that texture on a large sphere using `SphereGeometry`. Clouds are separate sprite objects.

### 31. Why is the sky sphere using `THREE.BackSide`?

The camera is inside the sky sphere. Normally a sphere shows its outside surface, but I need to see the inside surface. `THREE.BackSide` makes the inside of the sphere visible.

### 32. Did you import the sky image?

No. The sky is generated by JavaScript canvas, so no extra sky image is needed. The current scene uses the bright daylight sky and clouds.

### 33. How do the clouds move?

The clouds are `THREE.Sprite` objects using a transparent canvas cloud texture. Since the unlimited loop was stopped, the clouds stay in their initial positions unless `renderScene()` is called by user interaction.

### 34. Why does the rotor blur appear only after pressing `0`?

The blur disks are hidden at first. When the user presses `0`, the rotor animation starts and the transparent blur disks become visible. When the user presses `9`, the animation stops and the disks are hidden again.

## Short Explanation To Say In Viva

My project uses Three.js to create a 3D scene. I create a camera, renderer, lights, and a helipad. The helipad is made from a circular mesh with a canvas-generated texture that includes concrete noise, rings, and a large `H`. I load a realistic NASA Ingenuity Mars Helicopter model from `assets/models/ingenuity-mars-helicopter.glb` using `GLTFLoader` and `DRACOLoader`. I scale and position the model on the helipad. I added an asphalt access road, colored helipad landing lights, transparent rotor blur disks, textured ground, trees, and one large terminal/control building. For the sky, I generate a daylight canvas texture and apply it to the inside of a large sphere. The mouse controls a point light using Raycaster, camera keys control the view, arrow keys move the helicopter, `O/P` lift and lower it, `0` starts the rotor rotation, and `9` stops it.
