# Donders-Institute-Data

This organization holds datalad versions of some of the datasets published in the [Donders Repository](https://data.donders.ru.nl). Using [datalad](https://www.datalad.org) you can `clone` these datasets and you can `get` the files on request. Only the README, LICENSE and MANIFEST files are actually stored, all other files will be downloaded automatically upon request. 

Alternatively, you can access public datasets using webdav via <https://public.data.donders.ru.nl>.

You can also access restricted access datasets via <https://webdav.data.donders.ru.nl>, this require registering, logging in and explicitly agreeing to the collection-specific Data Use Agreement (DUA). See [here](https://data.donders.ru.nl/doc/help/faq/access-shared-data.html) for instructions. The restricted access collections are not included here.

## Getting all collections as submodules

You can checkout this repository, which includes all data collections as submodules.

    git clone https://github.com/Donders-Institute-Data/donders-institute-data.git
    cd donders-institute-data
    git submodule init
    git submodule update

Or you can combine these commands all at once with

    git clone --recurse-submodules https://github.com/Donders-Institute-Data/donders-institute-data.git

See the [submodules](https://git-scm.com/book/en/v2/Git-Tools-Submodules) documentation for details.

Subsequently you can download the specific files for a specific collection.

    cd <data_collection_id>
    datalad get 

See the [datalad handbook](http://handbook.datalad.org/en/latest/) for more details or `datalad --help`.

## Adding public collections

I am using the following `manifest_to_datalad` script to create and add new datalad repositories.

```
#!/bin/bash
#
# Use as:
#   manifest_to_datalad.sh <WEBDAVPREFIX> <GITHUBREPO>
#
# For example:
#   manifest_to_datalad https://public.data.donders.ru.nl/dcc/DSC_2019.00036_099_v1 git@github.com:Donders-Institute-Data/dcc.DSC_2019.00036_099_v1
#
# This builds on http://datalad.org/ and https://github.com/cli/cli
#
# Note that this is not a very robust script

WEBDAVPREFIX="$1"
GITHUBREPO="$2"

WEBDAVPREFIX=https://public.data.donders.ru.nl/dcc/DSC_2019.00036_099_v1
GITHUBREPO=git@github.com:Donders-Institute-Data/test.git

if [ -z "$WEBDAVPREFIX" -o -z "$GITHUBREPO" ] ; then
echo Use as: $0 '<WEBDAVPREFIX> <GITHUBREPO>'
exit 1
fi

# make a short name for the dataset
DATASET=$(basename "$GITHUBREPO" .git)
mkdir -p $DATASET
cd $DATASET

# these are downloaded prior to initializing the datalad dataset
# to ensure that they are reporesented as files, not as links

if [ ! -e MANIFEST.txt ] ; then wget "$WEBDAVPREFIX"/MANIFEST.txt ; fi
if [ ! -e README.txt   ] ; then wget "$WEBDAVPREFIX"/README.txt   ; fi
if [ ! -e LICENSE.txt  ] ; then wget "$WEBDAVPREFIX"/LICENSE.txt  ; fi

gh repo create --public Donders-Institute-Data/"$DATASET"

datalad create --force
git add MANIFEST.txt README.txt LICENSE.txt && git commit -am "added general metadata files"

cat MANIFEST.txt | while read LINE ; do
  LINE=($LINE)
  HASH=${LINE[@]:0:1}
  FILE=${LINE[@]:1}
  DIR=`dirname "$FILE"`
  BASE=`basename "$FILE"`
  if [ ! -e "$FILE" -a ! -L "$FILE" ]; then
    if [ ! -z "$DIR" ] ; then
      mkdir -p "$DIR"
    fi
    datalad download-url "$WEBDAVPREFIX"/"$FILE" -O "$FILE"
    datalad drop "$FILE"
  else
    #echo skipping "$FILE"
    echo -n
  fi
  if [ -e "$FILE" ] ; then
    echo file "$FILE" is not a link
  fi
done

git remote add origin "$GITHUBREPO"
git push --set-upstream origin master
datalad push --to origin
```
