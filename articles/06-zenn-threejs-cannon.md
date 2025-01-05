---
published: true
cssclasses:
  - zenn
type: tech
emoji: 🎱
title: Three.js + cannon-es + Tauriで3段クルーンをシミュレーションできるやつを作った話
topics: 
date: 2025-01-06
AutoNoteMover: disable
url: https://zenn.dev/thirdlf/articles/06-zenn-threejs-cannon
tags:
  - threejs
  - webgl
  - tauri
aliases:
---
# 概要
突然ですが、3段クルーンって見てて面白いですよね？
あれをシミュレーションできるアプリ作ったら面白いなと思い作りました。

実際に作ったやつ
https://zawa.thirdlf03.com/

レポ
https://github.com/thirdlf03/zawa

アプリ配布先
https://github.com/thirdlf03/zawa/releases

# 技術周り

- Tauri
- Three.js
- cannon-es
- cannon-es-debugger
- lil-gui

デプロイ
- Cloudflare Pages

## Tauri
今回、初めてTauriに触ったんですが便利でした。
フロントをjsやts、バックをrustで書けるのも魅力的なのと、Electronよりも軽量なのも良い

### 環境構築
今回は、bunで環境構築しました。
UI templeteはJS/TSのVanilla
```bash
bun create tauri-app
```

### アプリ配布
github actionsを使えば、お手軽に配布できます。

公式のexampleです
https://github.com/tauri-apps/tauri-action/tree/dev/examples

今回使ったactions
```yml
name: "publish"

on:
  push:
    branches:
      - main

jobs:
  publish-tauri:
    permissions:
      contents: write
    strategy:
      fail-fast: false
      matrix:
        include:
          - platform: "macos-latest" # for Arm based macs (M1 and above).
            args: "--target aarch64-apple-darwin"
          - platform: "macos-latest" # for Intel based macs.
            args: "--target x86_64-apple-darwin"
          - platform: "ubuntu-22.04"
            args: ""
          - platform: "windows-latest"
            args: ""

    runs-on: ${{ matrix.platform }}
    steps:
      - uses: actions/checkout@v4

      - name: setup node
        uses: actions/setup-node@v4
        with:
          node-version: lts/*

      - name: install Rust stable
        uses: dtolnay/rust-toolchain@stable
        with:
          targets: ${{ matrix.platform == 'macos-latest' && 'aarch64-apple-darwin,x86_64-apple-darwin' || '' }}

      - name: install dependencies (ubuntu only)
        if: matrix.platform == 'ubuntu-22.04' # This must match the platform value defined above.
        run: |
          sudo apt-get update
          sudo apt-get install -y libwebkit2gtk-4.0-dev libwebkit2gtk-4.1-dev libappindicator3-dev librsvg2-dev patchelf
      
      - uses: oven-sh/setup-bun@v2
      - name: install frontend dependencies
        run: bun install
      - name: build
        run: bun run build

      - uses: tauri-apps/tauri-action@v0
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        with:
          tagName: app-v__VERSION__ # the action automatically replaces \_\_VERSION\_\_ with the app version.
          releaseName: "App v__VERSION__"
          releaseBody: "See the assets to download this version and install."
          releaseDraft: true
          prerelease: false
          args: ${{ matrix.args }}
```

## Three.js + cannon-es + lil-gui
3dモデルを動かしたかったので、thress.jsと物理演算をするためにcannon-es
guiで値を操作するために、lil-guiを使用しています

### 環境構築
Typescriptように構築
```bash
bun install -D @types/three @types/cannon lil-gui cannon-es-debugger
```

### three.js + cannon-es
three.jsで3dモデルを描画する。それと全く同じものをcannonの物理演算できる空間上に配置し、物理演算の結果をthree.jsのモデルに反映されることで動いてるように見せている

## three.js + cannon-es example
three.jsとcannon-esを合わせるexample codeです。

@[codepen](https://codepen.io/thirdlf03/pen/xbKPvYz)


まずは、three.jsでボールを描写する。
### three.js

初期化
```ts
import * as THREE from 'three';
  
const scene = new THREE.Scene();  
```

カメラとレンダーを追加

```ts
const camera = new THREE.PerspectiveCamera(45, window.innerWidth / window.innerHeight);  
camera.position.set(0, -10, 60);  
scene.add(camera);
  
const canvasElement = document.querySelector('#Game') as HTMLCanvasElement;  
const renderer = new THREE.WebGLRenderer({ canvas: canvasElement });  
renderer.setSize(window.innerWidth, window.innerHeight);
```

カメラを操作できるようにする
```ts
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js';

const controls = new OrbitControls(camera, renderer.domElement);
```

ボールとボックスを用意
```ts
const ballGeometry = new THREE.SphereGeometry(5);  
const ballMaterial = new THREE.MeshNormalMaterial();  
const ballMesh = new THREE.Mesh(ballGeometry, ballMaterial);  
ballMesh.position.set(0, 0, 0);  
scene.add(ballMesh);

const boxGeometry = new THREE.BoxGeometry(10, 10, 10);  
const boxMaterial = new THREE.MeshNormalMaterial();  
const boxMesh = new THREE.Mesh(boxGeometry, boxMaterial);  
boxMesh.position.set(0, -20, 0);  
scene.add(boxMesh);

```

描写
```ts
animate();  
  
function animate() {  
    ballMesh.rotation.y += 0.01;  
    renderer.render(scene, camera);  
  
    requestAnimationFrame(animate);  
}

```

### cannon-es
初期化
```ts
import * as CANNON from 'cannon-es';

const GRAVITY = -9.81;

const world = new CANNON.World();
world.gravity.set(0, GRAVITY, 0);
```

ボールとボックスをcannonの世界に追加
```ts
const ballShape = new CANNON.Sphere(5);  
const ballBody = new CANNON.Body({ mass: 1, shape: ballShape });  
ballBody.position.set(0, 0, 0);  
world.addBody(ballBody);  
  
const boxShape = new CANNON.Box(new CANNON.Vec3(10, 10, 10));  
const boxBody = new CANNON.Body({ mass: 0, shape: boxShape });  
boxBody.position.set(0, -20, 0);  
world.addBody(boxBody);
```

three.jsの物体にcannonの物体の位置や回転をコピー
```ts
function animate() {  
    world.step(1 / 60);  
    ballMesh.rotation.y += 0.01;  
    renderer.render(scene, camera);  
  
    ballMesh.position.copy(ballBody.position);  
    ballMesh.quaternion.copy(ballBody.quaternion);  
  
    boxMesh.position.copy(boxBody.position);  
    boxMesh.quaternion.copy(boxBody.quaternion);  
  
    requestAnimationFrame(animate);  
}

```


これで動くようになったが、cannon-esの当たり判定が意図してないものになっている。
これを可視化するために、cannon-es-debuggerを追加する
### cannon-es-debugger
```ts
import CannonDebugger from 'cannon-es-debugger'

const cannonDebugger = CannonDebugger(scene, world, {  
    color: '#ff0000',  
});

function animate() {  
    world.step(1 / 60);  
    ballMesh.rotation.y += 0.01;  
    renderer.render(scene, camera);  
  
    ballMesh.position.copy(ballBody.position);  
    ballMesh.quaternion.copy(ballBody.quaternion);  
  
    boxMesh.position.copy(boxBody.position);  
    boxMesh.quaternion.copy(boxBody.quaternion);  
  
    cannonDebugger.update();  
  
    requestAnimationFrame(animate);  
}

```


fullcode
```ts
import * as THREE from 'three';  
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js';  
import * as CANNON from 'cannon-es';  
import CannonDebugger from 'cannon-es-debugger'  
  
const GRAVITY = -9.81;  
const scene = new THREE.Scene();  
const world = new CANNON.World();  
world.gravity.set(0, GRAVITY, 0);  
  
  
const camera = new THREE.PerspectiveCamera(45, window.innerWidth / window.innerHeight);  
camera.position.set(0, -10, 60);  
scene.add(camera);  
  
const canvasElement = document.querySelector('#Game') as HTMLCanvasElement;  
const renderer = new THREE.WebGLRenderer({ canvas: canvasElement });  
renderer.setSize(window.innerWidth, window.innerHeight);  
  
const controls = new OrbitControls(camera, renderer.domElement);  
  
//three.js  
const ballGeometry = new THREE.SphereGeometry(5);  
const ballMaterial = new THREE.MeshNormalMaterial();  
const ballMesh = new THREE.Mesh(ballGeometry, ballMaterial);  
ballMesh.position.set(0, 0, 0);  
scene.add(ballMesh);  
  
const boxGeometry = new THREE.BoxGeometry(10, 10, 10);  
const boxMaterial = new THREE.MeshNormalMaterial();  
const boxMesh = new THREE.Mesh(boxGeometry, boxMaterial);  
boxMesh.position.set(0, -20, 0);  
scene.add(boxMesh);  
  
//cannon.js  
const ballShape = new CANNON.Sphere(5);  
const ballBody = new CANNON.Body({ mass: 1, shape: ballShape });  
ballBody.position.set(0, 0, 0);  
world.addBody(ballBody);  
  
const boxShape = new CANNON.Box(new CANNON.Vec3(10, 10, 10));  
const boxBody = new CANNON.Body({ mass: 0, shape: boxShape });  
boxBody.position.set(0, -20, 0);  
world.addBody(boxBody);  
  
const cannonDebugger = CannonDebugger(scene, world, {  
    color: '#ff0000',  
});  
  
animate();  
  
function animate() {  
    world.step(1 / 60);  
    ballMesh.rotation.y += 0.01;  
    renderer.render(scene, camera);  
  
    ballMesh.position.copy(ballBody.position);  
    ballMesh.quaternion.copy(ballBody.quaternion);  
  
    boxMesh.position.copy(boxBody.position);  
    boxMesh.quaternion.copy(boxBody.quaternion);  
  
    cannonDebugger.update();  
  
    requestAnimationFrame(animate);  
}
```


# 詰まったところ
## OrbitControlsをimportできない
OrbitControlsをimportで詰まった

これだとエラーを吐く

```ts
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls';
```

これだとおk
```ts
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js';
```

## glbモデルの当たり判定調整
3dモデルをパーツごとに分割して読み込むのと、trimeshのスケールを調整することで対応しました。


## Cloudflare Pagesでbunが使えない
デフォルトの状態だとbunでbuildすることができずに困っていました。
解決策として、
envにbun_versionを入れるといいみたいです。

![](/images/article-6/06-pages.png)

# 感想
割といいものができて満足。
 RustにもThree-dやRapierといった3dモデルや物理演算を扱うモデルがあるのでこれらも触っていきたい。

