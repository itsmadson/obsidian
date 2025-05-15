**Install dependencies:**
```
go get github.com/gorilla/mux
go get github.com/mattn/go-sqlite3
```
**Run**
```
go run main.go
```
**Ex Requset**
```
GET http://localhost:8000/tiles/test/13/4819/2800.png
GET http://localhost:8000/tiles/{tilesets}/{Z}/{X}/{Y}.png
```

**Debug logs**
```
http://localhost:8080/debug
```