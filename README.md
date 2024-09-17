
based: https://github.com/bobbyroe/flythru-wireframe-wormhole

# Cosmic Tunnel

Cosmic Tunnel is an immersive 3D visual experience built using Three.js, creating a surreal journey through a glowing, futuristic tunnel. This project utilizes advanced 3D geometry, lighting, and post-processing techniques to deliver a dynamic and interactive visual effect, transporting users into an otherworldly environment.

## Key Features

Tube Geometry with Dynamic Spline Path

***1. The tunnel is generated using TubeGeometry, which follows a custom spline path defined in the spline.js file. The path is a series of CatmullRomCurve3 points, forming a twisting, turning passageway.
The geometry itself is highlighted using EdgesGeometry, giving the tunnel a clean, neon-like wireframe aesthetic that adds to the sci-fi look and feel.
Post-Processing with Bloom Effects***
```js
const curvePath = [10.136184463414924, -1.374508746897471, 10.384881573913269,
    9.1152593889854714, -1.374508746897471, 8.5846792797570011,
    9.0669355709754882, -1.0665123466336568, 5.8937771631608156,
    10.151040177840205, -0.65913653144937956, 3.4340491740541346,
    10.806779203170416, 1.8859391007298545, 0.46855774212986023,
    10.761433540147586, 2.8724172201359197, -1.2811838605587311,
    9.6195923104445065, 2.8724172201359197, -3.2833099941904766,
    6.9763020889151646, 2.7659257976905427, -4.7591958908830172,
    6.0461277891353697, 1.0727045302089879, -6.6638740164090482,
    7.3472235778544794, -1.8228856326635698, -9.0685043046185623,
    7.226367212900791, -1.8228856326635698, -10.499536640855691,
    5.8354566696263914, -1.8228856326635698, -12.039219379199908,
    3.6532357452141353, -0.20463983570573391, -13.87695442281038,
    -0.30169589630131455, 1.5965000671484342, -14.879986418947327,
    -2.8925694230502157, 2.2971364614427481, -13.892095587598131,
    -4.537672295357936, 4.5863515759659208, -12.140831652074551,
    -6.1287913464117594, 5.9653814634119815, -8.9776527318875896,
    -6.0120301606452813, 4.4081161943855998, -6.712084358394045,
    -5.2138252159038974, 2.820894808418279, -4.4532820412085607,
    -2.3424712835109611, 2.2032065005086259, -3.0788773693500198,
    -0.0076956453915433265, 1.8931797788880202, -1.6577070662471063,
    -0.24767503988481437, 2.8845808465856684, 0.073915859214221724,
    -2.2174044353598896, 4.2415524507318576, 2.215992718290742,
    -3.4526531678364756, 3.0615192023340851, 4.7922404932096558,
    -3.7356278971556445, 1.4054080369354316, 7.8432021841434629,
    -3.4003734463804118, 1.1924069108769393, 9.2464090886227073,
    -1.8851803760476225, 1.5269331003449989, 10.306083896408374,
    0.01071077144031829, 2.1101821577522295, 10.490880699847727,
    0.42562058195647001, 2.2759939598834387, 11.613129436580291,
    0.096405262182225115, 0.032317784084054391, 16.223455375061565,
    2.3458797884520433, 0.38907275257695584, 19.91188266079584,
    5.7018400098488771, 1.73337964747396, 20.615481586999959,
    7.9720939736751824, 1.73337964747396, 19.303399329816457,
    9.8672362721095652, 0.090083018057025177, 16.893338541618121,
    11.225959519544134, -1.374508746897471, 14.279002555560753,
    11.288646925965876, -1.374508746897471, 11.926359497447137,
    10.136184463414924, -1.374508746897471, 10.384881573913269
];

const points =[];
const len = curvePath.length;
for  (let p=0 ;p < len; p+=3 ){
    points.push(new THREE.Vector3(
        curvePath[p],
        curvePath[p+1],
        curvePath[p+2]
    ));

}

const spline = new THREE.CatmullRomCurve3(points);
export default spline;
```


***2.UnrealBloomPass adds a dramatic glow to the entire scene, intensifying the visual appeal of the tunnel by creating bright, saturated highlights. This bloom effect, combined with ACESFilmicToneMapping, creates a unique color tone reminiscent of a space tunnel.
By using EffectComposer and RenderPass, the render flow includes this bloom as a post-process, creating vivid contrasts and a striking atmosphere.
Floating Cubes***

```js
const renderScene=  new RenderPass(scene,camera);
const bloomPass = new UnrealBloomPass(new THREE.Vector2(w,h) ,1.5,0.4,1 )
bloomPass.threshold= 0.002;
bloomPass.strength = 3.5;
bloomPass.radius = 0;
const composer = new EffectComposer(renderer);
composer.addPass(renderScene);
composer.addPass(bloomPass);
```

***3. Along the tunnel, small floating cubes are randomly placed. These cubes rotate in all directions, creating dynamic movement as the user travels through the tunnel. They are rendered as wireframes using BoxGeometry and are carefully positioned along the tunnel using points from the tube geometry path.
The cubes also come with vibrant colors, created using HSL values to provide a continuous gradient effect along the tunnel's path.
Interactive Camera Motion***

```js
const tubeGeo =new THREE.TubeGeometry(spline, 222, 0.65, 16, true);
const edges = new THREE.EdgesGeometry(tubeGeo, 1);
const lineMat = new THREE.LineBasicMaterial({ color:"#00ffff" });
const tubeLines = new THREE.LineSegments(edges, lineMat);
scene.add(tubeLines);



const numBoxes = 55;
const size= 0.075;
const boxGeo = new THREE.BoxGeometry(size, size , size);
for (let i=0; i< numBoxes; i+=1) {
    const boxMat= new THREE.MeshBasicMaterial({
        color: "#39ff14",
        wireframe: true,
    });
    const box = new THREE.Mesh(boxGeo,boxMat);
    const p =( i/numBoxes + Math.random() * 0.1) % 1;
    const pos = tubeGeo.parameters.path.getPointAt(p);
    pos.x += Math.random() -0.4;
    pos.z += Math.random() -0.4;
    box.position.copy(pos);
    const rote = new THREE.Vector3(
            Math.random() * Math.PI,
            Math.random() * Math.PI,
            Math.random() * Math.PI,
    );

    box.rotation.set(rote.x, rote.y, rote.z);
    const edges = new THREE.EdgesGeometry(boxGeo, 0.2);
     const color = new THREE.Color().setHSL(0.7 - p, 1, 0.5);
    const lineMat = new THREE.LineBasicMaterial({ color})
    const boxLines = new THREE.LineSegments(edges, lineMat);
    boxLines.position.copy(pos);
    boxLines.rotation.set(rote.x, rote.y, rote.z);
    //scene.add(box);
    scene.add(boxLines);
}
```

***4.The camera moves dynamically along the tunnel by following the spline's path. This smooth movement gives the viewer the sensation of flying through a futuristic wormhole. As the camera moves, it also looks ahead to the upcoming sections of the tunnel, creating a continuous forward motion.
The user can interact with the camera's movement using OrbitControls, providing control over rotation and smooth transitions as the camera moves along the tunnel.
Atmospheric Lighting and Fog***

```js
function updateCamera(t) {
    const time = t * 0.1;
    const looptime = 10 * 1000;
    const p = (time % looptime) / looptime;
    const pos = tubeGeo.parameters.path.getPointAt(p);
    const lookAt = tubeGeo.parameters.path.getPointAt((p + 0.03) % 1);
    camera.position.copy(pos);
    camera.lookAt(lookAt);

}
```


***5. A HemisphereLight brightens the scene, making the colors pop against the dark tunnel. This lighting enhances the visual depth and makes the environment more engaging.
The scene is also filled with a thick fog using FogExp2, which gives the tunnel a mysterious, endless depth as distant objects gradually fade into the background.
Responsive Design***

```js
const hemiLights = new  THREE.HemisphereLight(0xffffff, 0x444444,2 );
scene.add(hemiLights);
```

The project is fully responsive, automatically adjusting the renderer's size when the browser window is resized. This ensures that the visual experience is consistently sharp and immersive across various screen sizes.

### Awesome Effect


![cosmic-tunnel](https://github.com/user-attachments/assets/d5978614-feb1-48ac-ad0a-a17c64e410e8)



