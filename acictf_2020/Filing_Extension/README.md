# Filing Extension #

**Category:**	Forensics  
**Points:**	10

**Prompt:** 
* We went to apply for a tax-filing extension with the IRS, and they replied with this image saying it contained the code we needed. Unfortunately, our computer refuses to display it and just says that it isn't a PNG... 
* [flag.png](./flag.png)

**Hints:** 
* Is the extension the only way to tell a file's type?
* Wouldn't it be awesome if there was a [list](https://en.wikipedia.org/wiki/List_of_file_signatures) of way to identify files.

**Solution:**
* Find out what type of file it is:
```
file flag.png
```
* It's a zip.  Change the extension
```
mv ./flag.png ./flag.zip
unzip flag.zip
cat flag.txt
```
* ACI{Something_witty_072f6a6f}
