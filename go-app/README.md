# Build out a hello world web app - in Go GoLang - with MINIMAL container size

Turns out the most minimal container is scratch (literally nothing) - since docker is running on linux / amd64 if I statically compile a go program to that architecture - it should just work.

```
package main

import (
	"fmt"
	"net/http"
)

func handler(w http.ResponseWriter, r *http.Request) {
	fmt.Fprintf(w, "Hi there, I love %s!", r.URL.Path[1:])
}

func main() {
	http.HandleFunc("/", handler)
	http.ListenAndServe(":8080", nil)
}
```
go build command
```
CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -v -a -installsuffix cgo -o main . 
```

(I'm not sure if CGO_ENABLED is really required since 1.5)

FROM scratch
ADD main /
CMD ["main"]
```

Super minimal.
