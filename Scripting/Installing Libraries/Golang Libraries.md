If an exploit requires a library that you don't have, you try the following:
1. Run `go install <library>`
2. Go the website, download the library, create a folder for it in `/usr/lib/go-1.18/src/` and add the .go files to that folder. 
	- For example, for the library `golang.org/x/crypto/pbkdf2` I went to `https://pkg.go.dev/golang.org/x/crypto/pbkdf2`, downloaded the `.go` source files, and uploaded in `/usr/lib/go-1.18/src/pbkdf2`)