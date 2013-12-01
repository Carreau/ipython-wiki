# How to Compile .less Files

For testing your development work in CSS, you'll need to compile the .less files, the dynamic style sheet language. Make sure you have dependencies that LESS requires, including fabric, node, npm, and lessc. Follow the below steps to compile the .less files:

```
$ cd ipython/IPython/html
$ fab css
[localhost] local: components/less.js/bin/lessc -x  style/style.less style/style.min.css
[localhost] local: components/less.js/bin/lessc -x  style/ipython.less style/ipython.min.css

Done
```

Alternatively, go to the repository root of ipython: 

```
python setup.py css
```