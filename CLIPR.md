CLI Plugin Repository (CLIPR)
=============

CLIPR is a server that the [CloudFoundry CLI](https://github.com/cloudfoundry/cli) 
can interact with to browse and install plugins. This documentation covers how to run CLIPR itself,
as well as how to create your own implementation if desired.

Running CLIPR
=============

#####Running as a Cloud Foundry App

1. `git clone` or `go get` this repo
  - To Git clone, you need to have [git](http://git-scm.com/downloads) installed
    Clone this repo `git clone https://github.com/cloudfoundry-incubator/cli-plugin-repo`
  
1. Push CLIPR to your instance of Cloud Foundry by doing `cf push` at the root of CLIPR

#####Running as a stand along binary

1. Install [Go](https://golang.org)
1. [Ensure your $GOPATH is set correctly](http://golang.org/cmd/go/#hdr-GOPATH_environment_variable)
1. Use `go build` to build the binary
  1. Include the path "$<REPO_LOCATION>/Godeps/_workspace" in the environment var $GOPATH, here is an example in OSX/Linux
  ```
    $ SET GOPATH=$HOME/user/go/src/github.com/cloud-foundry/cli-plugin-repo/Godeps/_workspace:$GOPATH
    $ go build -o clipr main.go
  ```
  1. this will produce a executable called `clipr`
1. Invoke the binary `./clipr` with the following options
  - `-n`: IP Address for the server to listen on, default is `0.0.0.0`
  - `-p`: Port number for the server to listen on, default is `8080`


Once you have a CLIPR server running, either as a Cloud Foundry app or a stand alone server
- Add the running server to your CLI via the `add-plugin-repo` command, for example, `cf add-plugin-repo local-repo http://localhost:8080`
- Browse & install plugins from CLI. Alternatively, you can point your browser at the server address, for example, `http://localhost:8080` to see a listing of plugins

Forking the repository for development
======================================

1. Install [Go](https://golang.org)
1. [Ensure your $GOPATH is set correctly](http://golang.org/cmd/go/#hdr-GOPATH_environment_variable)
1. Install [godep](https://github.com/tools/godep)
1. Get the cli source code: `go get github.com/cloudfoundry/cli`
  * (Ignore any warnings about "no buildable Go source files")
1. Run `godep restore` (note: this will modify the dependencies in your $GOPATH)
1. Fork the repository
1. Add your fork as a remote: `cd $GOPATH/src/github.com/cloudfoundry/cli && git remote add your_name https://github.com/your_name/cli`

  
Creating your own Plugin Repo Server
=============
Alternatively, you can create your own plugin repo implementation. The server must meet the requirements:
- server must have a `/list` endpoint which returns a JSON object that lists the plugin info in the correct form
```
{"plugins": [
  {
    "name":"echo",
    "description":"echo repeats input back to the terminal",
    "version":"0.1.4",
    "date":"0001-01-01T00:00:00Z",
    "company":"",
    "author":"",
    "contact":"feedback@email.com",
    "homepage":"http://github.com/johndoe/plugin-repo",
    "binaries": [
      {
        "platform":"osx",
        "url":"https://github.com/johndoe/plugin-repo/raw/master/bin/osx/echo",
        "checksum":"2a087d5cddcfb057fbda91e611c33f46"
      },
      {
        "platform":"windows64",
        "url":"https://github.com/johndoe/plugin-repo/raw/master/bin/windows64/echo.exe",
        "checksum":"b4550d6594a3358563b9dcb81e40fd66"
      }
    ]
  }
]}
```
* To see a live example of the JSON object, browse to http://plugins.cloudfoundry.org/list