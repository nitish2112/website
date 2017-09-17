# To install hugo on your Ubuntu machine

```bash
% sudo apt-get install golang-go
% wget https://github.com/gohugoio/hugo/releases/download/v0.27.1/hugo_0.27.1_Linux-64bit.deb
% dpkg -i hugo_0.27.1_Linux-64bit.deb
```
Note you can also find other releases of hugo on:

```bash
https://github.com/gohugoio/hugo/releases/
```

# These are the steps to download this website and build it on Ubuntu machine

```bash
% git clone git@github.com:nitish2112/website.git
% cd website
% git submodule update --init
% git clone git@github.com:nitish2112/nitish2112.github.io.git public
```

Now you can make your chnages in the website/contents folder and run the 
following commands to view your website locally

```bash
% cd website
% hugo server -w
```
 
Go to your browser and type http://localhost:1313/ in the URL and you should 
be able to view your website.

# To commit your changes to the website and make them public

```bash
% cd public
% git commit -a -m "XYZ"
& git push
```
This will push your repository to nitish2112.github.io and the changes in your
website will be available publicly.

# To commit the changes in the markdown files 

```bash
% cd website
% git commit -a -m "ABC"
% git push
```

This will push the changes to website repo so that you can clone your hugo
project on any machine.
