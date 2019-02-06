---
layout: article
title: Uploading files to File.io with Python, Requests and Click
---

I recently discovered [file.io](https://file.io), a tool that allows you to upload and share links to files that self-destruct once they're accessed.

I thought this would be a good opportunity to try building out a CLI using Click. 

## The plan

I want my app to do 2 things: 

1. Accept a `filename` argument and an optional `--expiry` flag that allows me to set a custom expiry date
2. Submit a POST request to https://file.io, returning a success message, a link to the file, and the expiry period.

## Dependencies

For the app to build, we'll need to install [Requests](http://docs.python-requests.org/en/master/) and [Click](https://click.palletsprojects.com/en/7.x/). 

**Using pip**

`pip install requests click`

Since we'll be running this from the command line, we will also need to use [setuptools](https://setuptools.readthedocs.io/en/latest/) to build the executable. 

`pip install setuptools`

## Project structure

In the interest of keeping things simple, our final project structure will look like this:

```
<fancy-upload>
-- upload.py 
-- setup.py # setuptools config
-- test.pdf # test file for upload
```

1. Create the directory via the command line

```
$ mkdir fancy-upload && cd fancy-upload
touch upload.py setup.py test.pdf   
```

2. Using your preferred editor, open `upload.py` and create the base file layout.


```
import requests

def main():
  # Your app logic goes here
  pass

if __name__ == "__main__":
  main()
```

## curl -F as a Python Request

For file.io to send us a working link, we need to understand what kind of data it is expecting. 

The file.io docs tell us how to format the request using curl: 

`curl -F "file=@test.txt" https://file.io`

From the [docs for 'curl -F'](https://curl.haxx.se/docs/manpage.html#-F):

> For HTTP protocol family, this lets curl emulate a filled-in form in which a user has pressed the submit button. This causes curl to POST data using the Content-Type multipart/form-data

This means file.io is expecting a POST request with `Content-Type multipart/form-data` 

The payload is `file=@test.txt` which tells the form to send a file. By appending the filename with `@<filename>` we are telling the content to send a binary file, as opposed to parsing the contents of the file.

The [python-requests documentation](http://docs.python-requests.org/en/master/user/quickstart/#post-a-multipart-encoded-file) provides a snippet telling us how to structure a Multipart-encoded file request:

```
url = 'https://file.io'
files = {
  'file': open('test.pdf', 'rb')}

r = requests.post(url, files=files)

r.text
```

## Testing the request

Putting it together so far, we should now have an `upload.py` file that looks something like this:


```
import requests

def main():
  url = 'https://file.io'

  files = {
    'file': open('test.pdf', 'rb')}

  r = requests.post(url, files=files)

  print(r.text)

if __name__ == "__main__":
  main()
```

Let's run it and see what happens. (Make sure you wrap `r.text` in a print() statement to see the output)

If everything went well, you should receive a `success` response: 

```
{"success":true,"key":"xXhLHp","link":"https://file.io/xXhLHp","expiry":"14 days"}
```

## Next time

If you made it this far, thanks! In Part 2, we will build upload.py into a CLI app using Click, and we will configure arguments that allow us to upload any file we'd like from the filesystem.

