# bf-alg-ndwi

bf-alg-ndwi is a library and a CLI for running shoreline detection on a directory of input data using an NDWI algorithm. The algorithm performs several steps including masking (if Landsat and BQA mask is provided), band ratioing, thresholding, and vectorizing to create a GeoJSON output of the in-scene coastline.

### Creating a Deploy Package

Docker can be used to create a deploy package for CentOS.  Call docker-compose to create the package in the deploy directory which can then be copied to the target system.

	$ docker-compose run package

Zip up the deploy directory and copy to the target machine. In the example below it is copied to the /work directory

	$ cd deploy; zip -ru package.zip ./

	# on target machine, copy to work directory
	$ unzip package.zip

There are two environment variables that need to be set prior to calling the CLI.

	$ export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/work/lib:/work/lib64:/work/lib64/mysql:/work/lib64/atlas
	$ export PYTHONPATH=$PYTHONPATH:/work/lib/python2.7/site-packages:/work/lib64/python2.7/site-packages

Call the bfalg-ndwi CLI:

```
$ python bfalg-ndwi.py -h

usage: bfalg_ndwi.py [-h] [--fout FOUT] [--outdir OUTDIR] [--qband QBAND]
                     [--coastmask]
                     green nir

Beachfront Algorithm: NDWI

positional arguments:
  green            Green (or Blue or Coastal Band)
  nir              NIR Band

optional arguments:
  -h, --help       show this help message and exit
  --fout FOUT      Output filename for geojson (default: coastline.geojson)
  --outdir OUTDIR  Save intermediate files to this dir (otherwise temp)
                   (default: )
  --qband QBAND    Quality band (used to mask clouds) (default: None)
  --coastmask      Mask non-coastline areas (default: False)
  ```


### Branches
The 'develop' branch is the default branch and contains the latest accepted changes to the code base. Changes should be created in a branch and Pull Requests issues to the 'develop' branch. Releases (anything with a version number) should issue a PR to 'master', then tagged with the proper version using `git tag`. Thus, the 'master' branch will always contain the latest tagged release.

### Testing
Python nose is used for testing, and any new function added to the main library should have at least one corresponding test in the appropriate module test file in the tests/ directory.

### Docker
A Dockerfile and a docker-compose.yml are included for ease of development. The built docker image provides all the system dependencies needed to run the library. The library can also be tested locally, but all system dependencies must be installed first, and the use of a virtualenv is recommended.

To build the docker image use the included docker-compose tasks:

    $ docker-compose build

Which will build an image called 'bf-py'. Then the imgae can be run:

    # this will run the image in interactive mode (open bash script)
    $ docker-compose run bash

    # this willl run the tests using the locally available image
    $ docker-compose run test