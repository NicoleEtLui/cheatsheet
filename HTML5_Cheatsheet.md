# HTML 5

#### <DOCTYPE>

* Si vous souhaitez faire du HTML5, n'allez pas plus loin et utilisez le doctype
simplifié : `<!doctype html>`.
[PLus d'infos sur les différents DOCTYPE.](https://www.alsacreations.com/article/lire/560-dtd-doctype-html-xhtml-comment-choisir.html)





#### <canvas></canvas>

```
<head>
...
	<script>
		window.onload = function() {
			var canvas = document.getElementById("drawingCanvas");
			var context = canvas.getContext("2d");
			//drawing goes here
		};
	</script>
</head>
...
<body>
  <canvas id="drawingCanvas" width="500" height="300"></canvas>
</body>
...
```
##### Remarque 

* une ligne ne se dessinera que lorsqu'on a déclaré contexte.stroke(), de même une forme n'apparaitra qu'après un fill();


##### function 

* `canvas.getContext()` renvoie un 'contexte de dessin' en fonction du type de contexte demandé (2d, webgl, ...)

* `context.moveTo()` déplace le point de dessin aux coordonnées (x, y) précisées.

* `context.closePath()` relie le dernier point ou on est allé au point défni par le dernier moveTo().

* `context.beginPath` permet de reset le moveTo, et donc de commencer une nouvelle forme et que un `closePath()` 
ne relierait pas les deux ensembles.


##### LoadImage 

* To draw an image using HTML5 Canvas, we can use the drawImage() method which requires an image object 
and a destination point. The destination point defines the top left corner of the image relative to the top 
left corner of the canvas.
Since the drawImage() method requires an image object, we must first create an image and wait for it 
to load before instantiating drawImage().  We can accomplish this by using the onload property of the image object.

```
<script>
...
      var imageObj = new Image();

      imageObj.onload = function() {
        context.drawImage(imageObj, 69, 50);
      };
      imageObj.src = 'http://www.html5canvastutorials.com/demos/assets/darth-vader.jpg';
    </script>
```

* To load the canvas with an image data URL, we can make an AJAX call to get a data URL, create an image object with the URL, 
and then draw the image onto the canvas with the drawImage() method of the canvas context.

```
<script>
      function loadCanvas(dataURL) {
        var canvas = document.getElementById('myCanvas');
        var context = canvas.getContext('2d');

        // load image from data url
        var imageObj = new Image();
        imageObj.onload = function() {
          context.drawImage(this, 0, 0);
        };

        imageObj.src = dataURL;
      }
      
      // make ajax call to get image data url
      var request = new XMLHttpRequest();
      request.open('GET', 'http://www.html5canvastutorials.com/demos/assets/dataURL.txt', true);
      request.onreadystatechange = function() {
        // Makes sure the document is ready to parse.
        if(request.readyState == 4) {
          // Makes sure it's found the file.
          if(request.status == 200) {
            loadCanvas(request.responseText);
          }
        }
      };
      request.send(null);

    </script>
```

* To crop an image using HTML5 Canvas, we can add six additional arguments to the drawImage() method, sourceX, 
sourceY, sourceWidth, sourceHeight, destWidth and destHeight. These arguments define the location and size of a 
rectangle that we want to cut out of an image.