# QuartusModelSimSetupLinux
Setup for Quartus and ModelSim on Linux. Useful for (for example) CPEN 211 and CPEN 311 at UBC

This was done on Pop!OS 19.04, an Ubuntu derivative

Doing it on Windows is substantially easier. 

Download your software [here](http://fpgasoftware.intel.com/17.1/)

Choose Lite unless you have a license

Make an account and follow the cues to download. 

I was unable to get Direct Download working. If you cannot either, please get a Windows machine somewhere and download the Linux .tar via Akamai DLM3. 

Move the tar to whatever directory and run ```tar -xvf <.tar file>```

I was unable to get the ./setup.sh script to work. It would finish installing, but the window would not terminate. Hence:

Install Quartus and ModelSim separately. Install Quartus with device support, but without Quartus Help stuff. 

Following works with Quartus Prime Lite Edition 17.1, but should work with minor modifications on other versions

***

FOR QUARTUS ```./QuartusLiteSetup-17.1.0.590-linux.run``` 

Verify that Quartus runs with ```./$QUARTUSHOME/bin/quartus ```

If you get this error:

```quartus: error while loading shared libraries: libpng12.so.0: cannot open shared object file: No such file or directory```

Build the package from source as per [here](https://askubuntu.com/questions/966757/libpng12-needed-for-17-10):

```
sudo apt-get install build-essential zlib1g-dev
wget https://github.com/glennrp/libpng/archive/v1.2.59.tar.gz
tar xvfz v1.2.59.tar.gz 
cd libpng-1.2.59/
./configure
make check
sudo make install
sudo ldconfig
```


If when running quartus you have an issue with any library (I had an issue with libstdc++), just replace the one in the quartus/linux64 directory with a symlink to the system version. 

***

FOR MODELSIM ```./vsim ``` 

Verify that ModelSim runs with ```./vsim ```

If you get this error: 

```could not find ./../linux_rh60/vsim```

Open vsim file with your text editor and change ```vco="linux_rh60"``` to ```vco="linux"```. Also change ```mode=${MTI_VCO_MODE:-""}``` to ```mode=${MTI_VCO_MODE:="32"}```

If you get this error: 

```Fatal: Read failure in vlm process (0,0)```

Build freetype 2.4.12 from [source](http://download.savannah.gnu.org/releases/freetype/freetype-2.4.12.tar.bz2)

```
./configure --build=i686-pc-linux-gnu "CFLAGS=-m32" "CXXFLAGS=-m32" "LDFLAGS=-m32"
make -j8
cd $MODELSIMHOME
mkdir lib32
cp $FREETYPEHOME/objs/.libs/libfreetype.so* ./lib32
```

Open vsim with your text editor and look for 


```
dir=`dirname $arg0`
```

After this line, add:

```
export LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:${dir}/lib32
```

If things still don't work, or if you get:

```
error while loading shared libraries: libXft.so.2: cannot open shared object file: No such file or directory
```
Try:

```
sudo apt install gcc-multilib g++-multilib lib32z1 lib32stdc++6 lib32gcc1 expat:i386 fontconfig:i386 libfreetype6:i386 libexpat1:i386 libc6:i386 libgtk-3-0:i386 libcanberra0:i386 libice6:i386 libsm6:i386 libncurses5:i386 zlib1g:i386 libx11-6:i386 libxau6:i386 libxdmcp6:i386 libxext6:i386 libxft2:i386 libxrender1:i386 libxt6:i386 libxtst6:i386 
```

If you get an issue where the built-in editor size has font size too small, open ```~/.modelsim``` and modify

```textFontV2 {{FONT} 12}``` into ```textFontV2 {{FONT} -12}```


