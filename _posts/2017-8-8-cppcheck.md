---
layout: post
title: Code check and html-report with cppcheck.
---

Cppcheck is a static analysis tool for C/C++ code. Project's home on sourceforge: [http://cppcheck.sourceforge.net/](http://cppcheck.sourceforge.net/).
Small description from the site:
>Unlike C/C++ compilers and many other analysis tools it does not detect syntax errors in the code. Cppcheck primarily detects the types of bugs that the compilers normally do not detect. The goal is to detect only real errors in the code (i.e. have zero false positives). 

So let's check some code with cppcheck and generate a html report with [CWE](https://cwe.mitre.org)-links in it.

At first you need to install cppcheck. Official site provides a source-code, but you may installed it via you favorite package manager. For example (fedora 25):
```
# dnf install cppcheck
```
Now, let's clone one of the famous project (also known as "A novices' nightmare") - vim
```
$ git clone https://github.com/vim/vim.git
$ cd vim
```
Assume, that we need to check files in ./src directory. For that you need to run something like this:
```
$ cppcheck --xml --xml-version=2 ./src 2>report-src.xml
```
As you see, I redirect stderr to the file report-src.xml. That's it, our report in XML-output. Let's see what's inside:
```
$ cat report-src.xml 

```
Following the [manual](http://cppcheck.sourceforge.net/manual.pdf), you can tune messages, that will be shown during check. Default is 'error'. To enable all the messages type
```
$ cppcheck --enable=all
```

### "But it's just ugly text file, what should I post on our internal wiki?"

Calm down! Cppcheck provides an awesome feature, that allows us to generate pretty nice HTML-reports.

For that we need to get `cppcheck-htmlreport` script. I found it [HERE](https://github.com/danmar/cppcheck/tree/master/htmlreport).
Now run something like this in vim's source directory:
```
$ mkdir VIMs_SRC && /path/to/htmlreport/cppcheck-htmlreport --source-dir=. --title=VIMs_SRC --file=report-src.xml --report-dir=VIMs_SRC
```
Let me explain arguments:
* --source-dir - directory with explored source
* --title - just title, which will be shown on html page
* --file - previously generated XML-report
* --report-dir - where to store HTML-report

When it's done, see what is in VIMs_SRC:
```
$ cd VIMs_SRC/
$ ls
0.html   12.html  15.html  1.html  4.html  7.html  index.html
10.html  13.html  16.html  2.html  5.html  8.html  stats.html
11.html  14.html  17.html  3.html  6.html  9.html  style.css
```
There is it! Open index.html with your favorite browser and you'll see the beautiful HTML-report with details and links to CWE-base! The results will be as following:
Index page of HTML-report:

![Cppcheck index](https://github.com/dunterov/dunterov.github.io/raw/master/images/cppcheck1.png "Index page")

Page with details:
![Cppcheck detail](https://github.com/dunterov/dunterov.github.io/raw/master/images/cppcheck2.png "Detail page")


As a conclusion, I want to say, that cppcheck is a very nice and useful tool. I use it as part of code quality check cycle at work.