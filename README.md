WARNING!  DO NOT USE IN IT'S CURRENT STATE.  THIS CODE HAS MULTIPLE SERIOUS VULNERABILITIES
Hi, 

Wanted to point out a couple security issues in your script.

**First, on line 128:**
`f.write("<br><a href=\"%s\">back</a>" % self.headers['referer'])`

The referer header is controlled by the visitor and potentially by an attacker.  Therefore, at the very least you should HTML entity escape quotes in it.  
```
try:
    cgi.escape(self.headers['referer'], True)
except:
    cgi.escape(self.headers['referer'], quote=True)
```

**Second, you have a serious path traversal flaw in line 155:**
`fn = os.path.join(path, fn[0])`

This is also user controlled.  If I pass a path like "../../../../../file.sh" or "..\..\..\..\..\..\..\..\..\" your full path will write outside the intended directory.

Can be fixed most easily like this:
`fn = os.path.join(path, fn[0].replace('\\', '/').split('/')[-1])`

**Third, you assume that Content-Length is accurate in line 144:**
`remainbytes = int(self.headers['content-length'])`

I don't see a way to to do serious damage with this, but it would be good to check that Content-Length is no larger than the total post length.

**Forth, I am unsure what "self.path" is, but I believe it is the directory where python is run from, when running the script.  If this is the same as the actual script, there is no protection to prevent an attacker from overwriting this script with his own code**

#Simple Python File Server With Browse, Upload, and Authentication
### What is this?
This is a simple file server that
* supports file directory browse of the server
* supports file upload to the server
* supports authentication
* marks frequently visited directories for easier navigation

It is tested with Ubuntu 14.04, python 2.7.6

Find the latest version at https://github.com/wonjohnchoi/Simple-Python-File-Server-With-Browse-Upload-and-Authentication

### Why did I make this?
Currently, I am in an environment where I am not allowed to install anything on my desktop for some security reason.

When using my toy aws ec2 instance, shellinabox was super useful because it gave secure remote shell access with no client-side installation.

But I could not find any simple tool that gave secure remote file control with no client-side installation.

So I forked off SimpleHTTPServerWithUpload written by bones7456 to make this tool.

I use this everyday in addition to shellinabox to have full control over my aws instance, and it's super convenient.

###How To Install
Read and edit settings.py.

`sudo ./install`

Once the script is completed, this file server should be registered as an upstart service.

Check the file server at http://host:port/base_url

###How To Uninstall
`sudo ./uninstall`

###Credit
This is a fork of http://li2z.cn/?s=SimpleHTTPServerWithUpload written by bones7456 (who also forked from http://www.opensource.apple.com/source/python/python-3/python/Lib/SimpleHTTPServer.py)

This fork basically adds install scripts, authentication, and some more on top of the original code that supports directory browse and file upload.
