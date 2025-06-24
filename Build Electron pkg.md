 Windows:
 ```
 npx electron-packager . Afsooneh --platform=win32 --arch=x64 --icon=./afsoone.ico --out=dist --asar --overwrite
```
 
Linux:
 ```
  npm install --save-dev electron-builder
```

Package.json:
 ```
{
  "name": "pomoson",
  "version": "1.0.0",
  "main": "main.js",
  "build": {
    "appId": "com.madson.pomoson",
    "productName": "Pomoson",
	"linux": {  
	  "target": ["deb"],  
	  "icon": "pomoson.png",  
	  "category": "Utility",  
	  "maintainer": "Madson"  
	}
  },
  "scripts": {
    "dist": "electron-builder"
  }
}

```

```
npm run dist
```

```
npx electron-builder --linux deb
```