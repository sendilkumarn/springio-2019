# Step 1
Install Nodejs.

[link](https://nodejs.org/en/)

If you have nodejs installed, skip to step 2

# Step 2
Check the nodejs version installed.

`node -v`

(make sure that the version is greater than 10)


# Step 3

Install generator-jhipster as a global module.

`npm i -g generator-jhipster`

if you get `EACCESS` error most probably you have file permissions issue. Try running

`sudo npm i -g generator-jhipster`

### If you have yarn installed in the system.

You can also run

`yarn add global generator-jhipster`

# Step 4

Check whether the installation is done correctly.

`jhipster --version`

:tada: we have completed the first milestone :tada:
