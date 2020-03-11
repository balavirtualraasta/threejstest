<!DOCTYPE html>
<html lang="en">
  <head>
    <title>Unity Three.js JSON Exporter - Demo</title>
    <meta charset="utf-8">
    <meta property="og:title" content="Unity to three.js Exporter" />
    <meta property="og:url" content="http://threejsexporter.nickjanssen.com/" />
    <meta property="og:image" content="http://threejsexporter.nickjanssen.com/images/og-image.png" />
    <link href="css/magnific-popup.css" rel="stylesheet" />
    <link href="css/demo.css" rel="stylesheet" />
  </head>
  <body>
    <div id="blocker">
      <div id="instructions">
        <div class="spinner">
          <div class="double-bounce1"></div>
          <div class="double-bounce2"></div>
        </div>
      </div>
    </div>

    <button class="view-unity-scene">View Unity Scene</button>

    <button class="download-zip">Download Demo Archive</button>

    <div class="changelog">
      <iframe src="https://docs.google.com/document/d/1H9vKY0JmplnizOQT7eYrC5wk91BsCfSEu_v6pGOdnYk/pub?embedded=true"></iframe>
    </div>

    <div class="header">
      <p>Three.js JSON Exporter for Unity - Buy from <a href="http://u3d.as/content/nick-janssen/three-js-json-exporter" target="_blank">Unity Asset Store</a> | <a href="https://sellfy.com/p/zW4m/" target="_blank">Sellfy</a></p>
      Demo: <a href="javascript:void(0)" id="SimpleScene" onclick="loadScene('SimpleScene')">Simple Scene</a> | <a href="javascript:void(0)" id="ForThree" onclick="loadScene('VikingHouse')">VikingHouse</a> | <a href="javascript:void(0)" id="Classroom" onclick="loadScene('Classroom')">Classroom</a> | <a href="javascript:void(0)" id="Car" onclick="loadScene('Car')">Car</a> |<a href="javascript:void(0)" id="bala" onclick="loadScene('bala')">bala</a>
    </div>

    <script src="js/jquery-1.11.3.min.js"></script>
    <script src="js/jquery.magnific-popup.min.js"></script>
    <script src="js/three.r81.js"></script>
    <script src="js/OrbitControls.js"></script>

    <script>

      var camera, scene, renderer, controls;
      var mesh;
      var loader;

      var blocker = document.getElementById( 'blocker' );

      var currentScene = null;
      var isTestSceneLoaded = false;

      var scenes = {
        'SimpleScene': {
          url: 'SimpleScene/scene.json',
          clearColor: 0xe3fff1,
          startPosition: new THREE.Vector3(6, 6, 6),
          lookAt: new THREE.Vector3(0, 0, 0)
        },
        'VikingHouse': {
          url: 'VikingHouse/scene.json',
          clearColor: 0xffd1d1,
          startPosition: new THREE.Vector3(12, 12, 12),
          lookAt: new THREE.Vector3(0, 0, 0)
        },
        'test': {
          url: 'test/scene.json',
          clearColor: 0xcdfffc,
          startPosition: new THREE.Vector3(10, 10, 10),
          lookAt: new THREE.Vector3(0, 0, 0),
          startFn: function (scene, renderer) {
            setTimeout(function () {
              isTestSceneLoaded = true
            }, 1000);
          }
        },
		'bala': {
          url: 'bala/scene.json',
          clearColor: 0xcdfffc,
          startPosition: new THREE.Vector3(10, 10, 10),
          lookAt: new THREE.Vector3(0, 0, 0),
          startFn: function (scene, renderer) {
            setTimeout(function () {
              isTestSceneLoaded = true
            }, 1000);
          }
        },
        'Classroom': {
          url: 'Classroom/scene.json',
          clearColor: 0xdbfff8,
          startPosition: new THREE.Vector3(3, 2, 3),
          lookAt: new THREE.Vector3(0, 2, 0)
        },
        'Car': {
          url: 'Car/scene.json',
          clearColor: 0x426986,
          startPosition: new THREE.Vector3(5, 5, 5),
          lookAt: new THREE.Vector3(0, 0, 0),
          startFn: function (scene) {
            scene.traverse(function (obj) {
              if (obj instanceof THREE.SpotLight) {
                obj.target.position.z = 5;
                obj.target.updateMatrixWorld();
              }
            });
          }
        }
      };

      $(document).ready(function() {
        $('.view-unity-scene').click(function () {
          $.magnificPopup.open({
            items: {
              src: 'images/editor/' + currentScene + '.png'
            },
            type: 'image'
          }, 0);
        });

        $('.download-zip').click(function () {
          location.href = 'demo.zip';
        });
      });

      var loadScene = function (sceneName) {

        currentScene = sceneName;

        blocker.style.opacity = 1.0;

        loader = new THREE.ObjectLoader();

        THREE.Cache.clear();
        setTimeout(function () {
          renderer.setClearColor(scenes[sceneName].clearColor);
          if (mesh) {
            scene.remove(mesh);
          }
          loader.load('scenes/' + scenes[sceneName].url + '?v1_1', function (obj) {
            mesh = obj;
            scene.add(mesh);
            blocker.style.opacity = 0.0;

            if (scenes[sceneName].startFn) {
              scenes[sceneName].startFn(scene, renderer);
            }

            controls.reset();

            camera.position.copy(scenes[sceneName].startPosition);
            camera.lookAt(scenes[sceneName].lookAt);
            controls.target.copy(scenes[sceneName].lookAt);
          });
        }, 500)
      };

      init();
      animate();

      function init() {
        renderer = new THREE.WebGLRenderer();
        renderer.setPixelRatio( window.devicePixelRatio );
        renderer.setSize( window.innerWidth, window.innerHeight );

        document.body.appendChild( renderer.domElement );

        camera = new THREE.PerspectiveCamera( 70, window.innerWidth / window.innerHeight, 1, 1000 );

        scene = new THREE.Scene();

        controls = new THREE.OrbitControls( camera );
        controls.damping = 0.2;

        window.addEventListener( 'resize', onWindowResize, false );

        loadScene('SimpleScene');
      }

      function onWindowResize() {

        camera.aspect = window.innerWidth / window.innerHeight;
        camera.updateProjectionMatrix();

        renderer.setSize( window.innerWidth, window.innerHeight );

      }



      function animate() {
        if (currentScene !== 'test' || !isTestSceneLoaded) requestAnimationFrame( animate );
        renderer.render( scene, camera );
      }

    </script>
    <script>
      (function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
      (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
      m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
      })(window,document,'script','//www.google-analytics.com/analytics.js','ga');

      ga('create', 'UA-64702519-2', 'auto');
      ga('send', 'pageview');

    </script>
  </body>
</html>
