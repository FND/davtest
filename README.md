testing whether GitHub supports WebDAV via its
[Subversion bridge](https://help.github.com/articles/support-for-subversion-clients/)

```bash
$ svn co --depth empty https://github.com/FND/davtest
$ cd davtest
$ svn up trunk
```

Note that Subversion uses HTTP, e.g.

```bash
$ nc -l 9999 &
$ svn log http://localhost:9999/FND/davtest/README.md
...
OPTIONS /FND/davtest/README.md HTTP/1.1
...
DAV: http://subversion.tigris.org/xmlns/dav/svn/depth
DAV: http://subversion.tigris.org/xmlns/dav/svn/mergeinfo
DAV: http://subversion.tigris.org/xmlns/dav/svn/log-revprops
...
```

However, from the developer console at github.com:

```javascript
jQuery.ajax({
	type: "PUT",
	url: "https://github.com/FND/davtest/README.md",
	data: "PUT successful",
	headers: {
		DAV: "http://subversion.tigris.org/xmlns/dav/svn/depth"
	}
});
```

This results in a 404 error, so presumably GitHub uses some other heuristic to
distinguish Subversion operations from regular HTTP requests.

Of course `svn commit` is different from `svn log`, so assumptions based on the
aforementioned `OPTIONS` request might not be valid for `PUT` (e.g. the `DAV`
request header value).
