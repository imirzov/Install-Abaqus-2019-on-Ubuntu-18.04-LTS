# Install Abaqus 2019 on Ubuntu 18.04 LTS


## INSTALLATION

From downloaded archives untar all to */opt/Abaqus*, and merge all duplicates.  
*/opt/Abaqus* should have folders '1', '2' etc. inside.

    sudo chmod -R 777 /opt/Abaqus/*

Copy *libpng12.so.0* to all folders with *DSYInsAppliGUI*:

    //linux_a64/code/bin/

Install prerequisites:

    sudo apt install ksh gcc g++ gfortran libstdc++5 build-essential make libjpeg62
    sudo apt install libmotif-dev libmotif-common rpm

Install license to */opt/SIMULIA/License*. Restart computer, license should start.  
Or later configure network license server without restart.

<!--
For all /opt/Abaqus//Linux.sh OS release:
    DSY_OS_Release=`lsb_release --short --id |sed 's/ //g'`
override with:
    DSY_OS_Release="RedHatEnterpriseWorkstation"
-->

Disable system checks:
<!--
    export DSY_Skip_CheckPrereq=1 DSY_IgnoreError_CheckPrereq=1
-->
    export DSYAuthOS_`lsb_release -si`=1 DSY_Force_OS=linux_a64

Run GUI for the installation:

    /opt/Abaqus/1/StartGUI.sh

Then follow instructions but skip licensing configuration. Install product to:

    /opt/DassaultSystemes/SimulationServices/V6R2019x

    /opt/SIMULIA/CAE/2019

Connect Abaqus to online documentation and license:

    gedit /opt/DassaultSystemes/SimulationServices/V6R2019x/linux_a64/SMA/site/custom_v6.env

```python
    doc_root="http://help.3ds.com"
    license_server_type=FLEXNET
    abaquslm_license_file="27800@127.0.0.1"
```



## FOLDERS AND SHORTCUTS

Make abaqus command available from any directory:

    sudo ln /opt/SIMULIA/Commands/abaqus /usr/bin/abaqus

<!--
Create temp directory:
sudo mkdir /opt/SIMULIA/Temp

Run this command before each start:
export TMPDIR=/opt/SIMULIA/Temp
or update onCaeStartup in /opt/DassaultSystemes/SimulationServices/V6R2019x/linux_a64/SMA/site/custom_v6.env

Create plugins directory:
sudo mkdir /opt/SIMULIA/CAE/2019/plugins
-->

If there is any warning regarding OpenGL in the terminal during start,  
simply run Abaqus with *-mesa* parameter:

    abaqus cae -mesa
    abaqus view -mesa

Create shortcut for CAE:

    gedit ~/.local/share/applications/abaquscae.desktop

```desktop
    [Desktop Entry]
    Type=Application
    Version=1.0
    Name[en_US]=abaquscae
    Icon=/opt/SIMULIA/CAE/2019/linux_a64/CAEresources/graphic/icons/icoR_application.png
    Exec=sh -c "export FILE=%u && cd $(dirname $FILE) && abaqus cae -mesa database=$FILE"
    # Exec=abaqus cae -mesa database=%u
    Terminal=true
    Categories=Science;
```

Create shortcut for Viewer:

    gedit ~/.local/share/applications/abaqusviewer.desktop

```desktop
    [Desktop Entry]
    Type=Application
    Version=1.0
    Name[en_US]=abaqusviewer
    Icon=/opt/SIMULIA/CAE/2019/linux_a64/CAEresources/graphic/icons/icoR_application.png
    Exec=sh -c "export FILE=%u && cd $(dirname $FILE) && abaqus viewer -mesa database=$FILE"
    # Exec=abaqus view -mesa database=%u
    Terminal=true
    Categories=Science;
```

Then make *.desktop executable:  
*Properties-->Permissions-->Allow executing file as program*.


## MIMETYPES

Delete LibreOffice mime type for ODB:

    sudo gedit /usr/share/mime/packages/libreoffice.xml

and comment tag:

```xml
    <mime-type type="application/vnd.sun.xml.base">
```

Add Abaqus mime types:

    sudo gedit ~/.mime.types

Add lines:

    application/abaquscae				cae
    application/abaqusviewer			odb

Create file:

    sudo gedit /usr/share/mime/packages/abaquscae.xml

```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <mime-info xmlns="http://www.freedesktop.org/standards/shared-mime-info">
        <mime-type type="application/abaquscae">
            <comment>Abaqus CAE</comment>
            <glob pattern="*.cae"/>
        </mime-type>
    </mime-info>
```

Create file:

    sudo gedit /usr/share/mime/packages/abaqusviewer.xml

```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <mime-info xmlns="http://www.freedesktop.org/standards/shared-mime-info">
        <mime-type type="application/abaqusviewer">
            <comment>Abaqus Viewer</comment>
            <glob pattern="*.odb"/>
        </mime-type>
    </mime-info>
```

Then update MIME database:

    sudo update-mime-database /usr/share/mime

<!--
Create icons for Abaqus file types:

    sudo cp /media/ihor/WORK/Soft/WORK/Abaqus/Info/3DS.svg /usr/share/icons/Humanity/mimes/256/application-abaquscae.svg
    sudo cp /media/ihor/WORK/Soft/WORK/Abaqus/Info/3DS.svg /usr/share/icons/Humanity/mimes/256/application-abaqusviewer.svg
    sudo gtk-update-icon-cache /usr/share/icons/Humanity -f
-->

Additionally install "MIME Type Editor" via software and edit file associations.


## FONTS

Open Abaqus CAE and check if it uses *Courier-New* and *Verdana* fonts:

    lsof -c ABQcaeK | grep fonts

Increase font size: https://kb.dsxclient.3ds.com/mashup-ui/page/resultqa?id=QA00000008675e

Use *xfontsel* to check if chosen font is available on system.

Install MS core fonts and RESTART:

    sudo apt install ttf-mscorefonts-installer
<!--
    sudo apt install xfonts-utils xfonts-100dpi xfonts-encodings
-->

Edit dictionaries:

    /opt/SIMULIA/CAE/2019/linux_a64/SMA/Configuration/Xresources/en_US/en_US_Dict.py
    /opt/DassaultSystemes/SimulationServices/V6R2019x/linux_a64/SMA/Configuration/Xresources/en_US/en_US_Dict.py

and increase font size:

```python
    masterDict[r'-*-courier-<weight>-<slant>-normal--10-*'] = r'-*-courier-<weight>-<slant>-normal--20-*'
    masterDict[r'-*-courier-<weight>-<slant>-normal--12-*'] = r'-*-courier-<weight>-<slant>-normal--25-*'
    masterDict[r'-*-courier-<weight>-<slant>-normal--14-*'] = r'-*-courier-<weight>-<slant>-normal--34-*'
    masterDict[r'-*-helvetica-<weight>-<slant>-normal--10-*'] = r'-*-helvetica-<weight>-<slant>-normal--20-*'
    masterDict[r'-*-helvetica-<weight>-<slant>-normal--12-*'] = r'-*-helvetica-<weight>-<slant>-normal--25-*'
    masterDict[r'-*-helvetica-<weight>-<slant>-normal--14-*'] = r'-*-helvetica-<weight>-<slant>-normal--34-*'
```

For big displays font HELVETICA and sizes 17-20-25 are more appropriate:

```python
    masterDict[r'-*-courier-<weight>-<slant>-normal--10-*'] = r'-*-helvetica-<weight>-<slant>-normal--17-*'
    masterDict[r'-*-courier-<weight>-<slant>-normal--12-*'] = r'-*-helvetica-<weight>-<slant>-normal--20-*'
    masterDict[r'-*-courier-<weight>-<slant>-normal--14-*'] = r'-*-helvetica-<weight>-<slant>-normal--25-*'
    masterDict[r'-*-helvetica-<weight>-<slant>-normal--10-*'] = r'-*-helvetica-<weight>-<slant>-normal--17-*'
    masterDict[r'-*-helvetica-<weight>-<slant>-normal--12-*'] = r'-*-helvetica-<weight>-<slant>-normal--20-*'
    masterDict[r'-*-helvetica-<weight>-<slant>-normal--14-*'] = r'-*-helvetica-<weight>-<slant>-normal--25-*'
```

<!--
#Enable fixed fonts in Ubuntu:
#http://marklodato.github.io/2014/02/23/fixed-fonts.html
-->

## FORTRAN

To use *gfortran* edit:

    /opt/DassaultSystemes/SimulationServices/V6R2019x/linux_a64/SMA/site/lnx86_64.env

as follows:

- Change ifort to gfortran on the fortCmd line and make sure that g++ is on the cppCmd line
- Remove gnu-incompatible compiler flags from the compile_fortran line (-V, -auto, -mP2OPT_hpo_vec_divbyzero=F, -extend_source, -fpp, -WB)
- link_sl: remove command line options ‘-V’, ‘-cxxlib’, ‘-threads’, ‘-parallel’, ‘-shared-intel’
- add '-lgfortran' to the link_sl and link_exe lines



## BUGS

Process hangs and doesn't finish:
- https://askubuntu.com/questions/1062058/process-hangs-before-termination-with-ubuntu-18-04
- https://github.com/Kevin-Mattheus-Moerman/Abaqus-Installation-Instructions-for-Ubuntu/issues/1


How to avoid strange Fox-windows? After each start delete file $HOME/abaqus_2019.gpr?


## TODO

Have a look on http://learningpatterns.me/posts-output/2018-01-30-abaqus-singularity/