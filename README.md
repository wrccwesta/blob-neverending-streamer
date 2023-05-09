# pixi-workshop-yggdrasil
Workshop template for pixi yggdrasil workshop



--

- use in combination of canvas stream (?)
- like favicon endless stream reload (?)

or corrupt local service workers, those of (any) extension in browser, and/or change local assets like browser 404 error images or chrome dino game:
- change local and/or browser assets by adapting metadata of it
- in canvas and/or color streaming by blurs/alpha/coloring
- by recaching them out of the supposed context (?)
- make never load or extreme slow load of some (browser)/worker assets, which will disable some very important security rules regarding cache



--



index.html
```html
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"
"http://www.w3.org/TR/html4/loose.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
        <title></title>
        
        <script src="ImageProxy.js"></script>
    </head>
    <body>
        <script>
        	var frameURL = "https://githubraw.com/wrccwesta/Imageproxy/master/ImageProxyFrame.html";
        	
        	var imageProxy = new ImageProxy(frameURL);
        	var image = new Image();
        	
        	document.body.appendChild(image);
        	
        	image.onload = function()
        	{
        		var canvas = document.createElement('canvas');
  				var context = canvas.getContext('2d');
 				canvas.width = this.width;
  				canvas.height = this.height;
  
				context.drawImage(this, 0,0);
				
				var imageData = context.getImageData(0,0, canvas.width,canvas.height);
				
				var d = imageData.data;
				
				for (var i=0; i < d.length; i+=4) 
				{
					var r = d[i];
				    var g = d[i+1];
				    var b = d[i+2];
				    
				    d[i] = g;
				    d[i+1] = b;
				    d[i+2] = r;
				}
				
				context.putImageData(imageData, 0, 0);
				
				document.body.appendChild(canvas);
        	}
        	
        	imageProxy.setImageSrc(image, "https://github.com/fluidicon.png");
        	
        </script>
        
       	
    </body>
</html>
```

ImageProxyFrame.html
```html
<!doctype html>

<head>
  <meta charset="utf-8">

  <title></title>
  
</head>

<body>
	<script>
		
		window.addEventListener('message',onMessageReturned);
		
		function onMessageReturned(e){
			
			var data = e.data;
		  	var image = new Image();
		  	
		  	image.onload = function()
			{
				canvas = document.createElement("canvas");
				canvas.context = canvas.getContext('2d');	
				canvas.width = this.width;
				canvas.height = this.height;
				canvas.context.drawImage(this, 0, 0);
				document.body.appendChild(canvas);
				
				var data = canvas.context.getImageData(0,0,canvas.width, canvas.height);
				var imageString = canvas.toDataURL("image/jpeg", 0.8);
				
				window.parent.postMessage(imageString, "*");
			}
		
			image.src = data;
		}

		window.parent.postMessage("loaded", "*");
	</script>
</body>
</html>



```
ImageProxy.js
```js

ImageProxy = function(frameUrl)
{
	this.iframe = document.createElement("iframe"); 
	
  	this.iframe.src = frameUrl;
  	
	this.iframe.style.width = 1 + "px"; 
	this.iframe.style.height = 1 + "px"; 
	this.iframe.style.top = 0 + "px";
	this.iframe.style.left = 0 + "px";
	this.iframe.style.position = "absolute";
	this.iframe.frameBorder = 0;	
	document.body.appendChild(this.iframe);
	
	window.addEventListener('message', this.onMessage.bind(this));
}

ImageProxy.constructor = ImageProxy;

ImageProxy.prototype.setImageSrc = function(image, src)
{
	this.image = image;
	
	this.src = src;
	
	if(this.target)
	{
		this.iframe.contentWindow.postMessage(this.src, "*");	
	}
}

ImageProxy.prototype.onMessage = function(event)
{
	if(event.data == "loaded")
	{
		this.target = event.source;
		
		if(this.src)
		{
			this.target.postMessage(this.src, "*");	
		}
	}
	else
	{
		this.image.src = event.data;
	}	
}
// create an instance!
```
