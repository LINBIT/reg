# reg

[![Travis CI](https://img.shields.io/travis/genuinetools/reg.svg?style=for-the-badge)](https://travis-ci.org/genuinetools/reg)
[![GoDoc](https://img.shields.io/badge/godoc-reference-5272B4.svg?style=for-the-badge)](https://godoc.org/github.com/genuinetools/reg)
[![Github All Releases](https://img.shields.io/github/downloads/genuinetools/reg/total.svg?style=for-the-badge)](https://github.com/genuinetools/reg/releases)

Docker registry v2 command line client.

- [Installation](#installation)
    + [Binaries](#binaries)
    + [Via Go](#via-go)
- [Usage](#usage)
    + [Auth](#auth)
    + [List Repositories and Tags](#list-repositories-and-tags)
    + [Get a Manifest](#get-a-manifest)
    + [Get the Digest](#get-the-digest)
    + [Download a Layer](#download-a-layer)
    + [Delete an Image](#delete-an-image)
    + [Vulnerability Reports](#vulnerability-reports)
    + [Using Self-Signed Certs with a Registry](#using-self-signed-certs-with-a-registry)
- [Contributing](#contributing)

## Installation

#### Binaries

For installation instructions from binaries please visit the [Releases Page](https://github.com/genuinetools/reg/releases).

#### Via Go

```console
$ go get github.com/genuinetools/reg
```

## Usage

```console
reg -  Docker registry v2 client.

Usage: reg <command>

Flags:

  -d              enable debug logging (default: false)
  -f              force allow use of non-ssl (default: false)
  -force-non-ssl  force allow use of non-ssl (default: false)
  -insecure       do not verify tls certificates (default: false)
  -k              do not verify tls certificates (default: false)
  -p              password for the registry (default: <none>)
  -password       password for the registry (default: <none>)
  -skip-ping      skip pinging the registry while establishing connection (default: false)
  -timeout        timeout for HTTP requests (default: 1m0s)
  -u              username for the registry (default: <none>)
  -username       username for the registry (default: <none>)

Commands:

  digest    Get the digest for a repository.
  layer     Download a layer for a repository.
  ls        List all repositories.
  manifest  Get the json manifest for a repository.
  rm        Delete a specific reference of a repository.
  tags      Get the tags for a repository.
  vulns     Get a vulnerability report for a repository from a CoreOS Clair server.
  version   Show the version information.
```

**NOTE:** Be aware that `reg ls` doesn't work with `hub.docker.com` as it has a different API then the [OSS Docker Registry](https://github.com/docker/distribution)

### Auth

`reg` will automatically try to parse your docker config credentials, but if
not present, you can pass through flags directly.

### List Repositories and Tags

**Repositories**

```console
# this command might take a while if you have hundreds of images like I do
$ reg ls r.j3ss.co
Repositories for r.j3ss.co
REPO                  TAGS
awscli                latest
beeswithmachineguns   latest
camlistore            latest
chrome                beta, latest, stable
...
```

**Tags**

```console
$ reg tags r.j3ss.co/tor-browser
alpha
hardened
latest
stable
```

### Get a Manifest

```console
$ reg manifest r.j3ss.co/htop
{
   "schemaVersion": 1,
   "name": "htop",
   "tag": "latest",
   "architecture": "amd64",
   "fsLayers": [
     {
       "blobSum": "sha256:a3ed95caeb02ffe68cdd9fd84406680ae93d633cb16422d00e8a7c22955b46d4"
     },
     ....
   ],
   "history": [
     ....
   ]
 }
```

### Get the Digest
```console
$ reg digest r.j3ss.co/htop
sha256:791158756cc0f5b27ef8c5c546284568fc9b7f4cf1429fb736aff3ee2d2e340f
```

### Download a Layer

```console
$ reg layer -o r.j3ss.co/chrome@sha256:a3ed95caeb02ffe68cdd9fd84406680ae93d633cb16422d00e8a7c22955b46d4
OR
$ reg layer r.j3ss.co/chrome@sha256:a3ed95caeb0.. > layer.tar
```


### Delete an Image

```console
$ reg rm r.j3ss.co/chrome@sha256:a3ed95caeb02ffe68cdd9fd84406680ae93d633cb16422d00e8a7c22955b46d4
Deleted chrome@sha256:a3ed95caeb02ffe68cdd9fd84406680ae93d633cb16422d00e8a7c22955b46d4
```

### Vulnerability Reports

```console
$ reg vulns --clair https://clair.j3ss.co r.j3ss.co/chrome
Found 32 vulnerabilities
CVE-2015-5180: [Low]

https://security-tracker.debian.org/tracker/CVE-2015-5180
-----------------------------------------
CVE-2016-9401: [Low]
popd in bash might allow local users to bypass the restricted shell and cause a use-after-free via a crafted address.
https://security-tracker.debian.org/tracker/CVE-2016-9401
-----------------------------------------
CVE-2016-3189: [Low]
Use-after-free vulnerability in bzip2recover in bzip2 1.0.6 allows remote attackers to cause a denial of service (crash) via a crafted bzip2 file, related to block ends set to before the start of the block.
https://security-tracker.debian.org/tracker/CVE-2016-3189
-----------------------------------------
CVE-2011-3389: [Medium]
The SSL protocol, as used in certain configurations in Microsoft Windows and Microsoft Internet Explorer, Mozilla Firefox, Google Chrome, Opera, and other products, encrypts data by using CBC mode with chained initialization vectors, which allows man-in-the-middle attackers to obtain plaintext HTTP headers via a blockwise chosen-boundary attack (BCBA) on an HTTPS session, in conjunction with JavaScript code that uses (1) the HTML5 WebSocket API, (2) the Java URLConnection API, or (3) the Silverlight WebClient API, aka a "BEAST" attack.
https://security-tracker.debian.org/tracker/CVE-2011-3389
-----------------------------------------
CVE-2016-5318: [Medium]
Stack-based buffer overflow in the _TIFFVGetField function in libtiff 4.0.6 and earlier allows remote attackers to crash the application via a crafted tiff.
https://security-tracker.debian.org/tracker/CVE-2016-5318
-----------------------------------------
CVE-2016-9318: [Medium]
libxml2 2.9.4 and earlier, as used in XMLSec 1.2.23 and earlier and other products, does not offer a flag directly indicating that the current document may be read but other files may not be opened, which makes it easier for remote attackers to conduct XML External Entity (XXE) attacks via a crafted document.
https://security-tracker.debian.org/tracker/CVE-2016-9318
-----------------------------------------
CVE-2015-7554: [High]
The _TIFFVGetField function in tif_dir.c in libtiff 4.0.6 allows attackers to cause a denial of service (invalid memory write and crash) or possibly have unspecified other impact via crafted field data in an extension tag in a TIFF image.
https://security-tracker.debian.org/tracker/CVE-2015-7554
-----------------------------------------
Unknown: 2
Negligible: 23
Low: 3
Medium: 3
High: 1
```

### Using Self-Signed Certs with a Registry

We do not allow users to pass all the custom certificate flags on commands
because it is unnecessarily messy and can be handled through Linux itself.
Which we believe is a better user experience than having to pass three
different flags just to communicate with a registry using self-signed or
private certificates.

Below are instructions on adding a self-signed or private certificate to your
trusted ca-certificates on Linux.

Make sure you have the package `ca-certificates` installed.

Copy the public half of your CA certificate (the one user to sign the CSR) into
the CA certificate directory (as root):

```console
$ cp cacert.pem /usr/share/ca-certificates
```

## Contributing

If you plan on contributing you should be able to run the tests locally. The
tests run for CI via docker-in-docker. But running locally with `go test`, you
need to make one modification to your docker daemon config so that you can talk
to the local registry for the tests.

Add the flag `--insecure-registry localhost:5000` to your docker daemon,
documented [here](https://docs.docker.com/registry/insecure/) for testing
against an insecure registry.

**OR** 

Run `make dind dtest` to avoid having to change your local docker config and
to run the tests as docker-in-docker.
