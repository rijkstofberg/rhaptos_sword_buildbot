Attribution:
Much of this is taken from:
https://software.cnx.rice.edu/svn/devsets/rhaptos-sword-service/README.ubuntu.txt

Above and beyond the normal libraries and packages necessary to run a Plone 3 development install tThe following system dependencies have to be installed before you start the buildbot installation:
* openjdk-6-jre python-virtualenv
* python2.4 python2.4-dev

OK, now we are ready to start:
Checkout the code:
Checkout git@github.com:rijkstorfberg/rhaptos_sword_buildbot.git rhaptos_sword_buildbot

cd rhaptos_sword_buildbot
virtualenv -p /usr/bin/python2.4 .

# now for the buildbot dependencies
virtualenv -p /usr/bin/python2.4 .
./bin/easy_install twisted
./bin/easy_install buildbot
./bin/easy_install buildbot-slave

# now we install the instance in the test slave
cd ./slave/runtests/build
virtualenv -p /usr/bin/python2.4 .
./bin/easy_install --find-links=http://download.zope.org/distribution PILwoTK
./bin/easy_install --find-links=http://initd.org/psycopg/tarballs psycopg2

# Install mxdatetime. Can't use easy_install for this because we need the
# header files later
cd downloads
wget http://downloads.egenix.com/python/egenix-mx-base-3.2.0.tar.gz
tar zxf egenix-mx-base-3.2.0.tar.gz
pushd egenix-mx-base-3.2.0
../../bin/python setup.py install
popd

# install psycopg
wget http://initd.org/psycopg/tarballs/PSYCOPG-1-1/psycopg-1.1.21.tar.gz
tar zxf psycopg-1.1.21.tar.gz
pushd psycopg-1.1.21
./configure --with-python=../../bin/python2.4 --with-postgres-includes=/usr/include/postgresql --with-mxdatetime-includes=../egenix-mx-base-3.2.0/mx/DateTime/mxDateTime
make
cp psycopgmodule.so ../../lib/python2.4/site-packages
popd

# Install libxslt
wget http://archive.ubuntu.com/ubuntu/pool/main/libx/libxslt/libxslt_1.1.26.orig.tar.gz
tar zxf libxslt_1.1.26.orig.tar.gz
pushd libxslt-1.1.26
touch libtoolT
./configure --prefix=/usr --with-python=/usr/bin/python2.4
make -C libxslt
make -C libexslt
make -C python
D=`mktemp -d /tmp/libxslt.XXXXXXXXXX`
DESTDIR=$D make -C python install
cp $D/usr/lib/python2.4/site-packages/libxslt{.py,mod.so} ../../lib/python2.4/site-packages
rm -rf $D
popd

# Install libxml2 libxml2mod
wget http://archive.ubuntu.com/ubuntu/pool/main/libx/libxml2/libxml2_2.7.6.dfsg.orig.tar.gz
tar zxf libxml2_2.7.6.dfsg.orig.tar.gz
pushd libxml2-2.7.6/python
../../../bin/python setup.py install
popd
cd ..
