# Donders-Institute-Data

This organization holds datalad versions of some of the datasets published in the [Donders Repository](https://data.donders.ru.nl). Using [datalad](https://www.datalad.org) you can `clone` these datasets and you can `get` the files on request. Only the README, LICENSE and MANIFEST files are actually stored, all other files will be downloaded automatically upon request. 

Alternatively, you can access public datasets using webdav via <https://public.data.donders.ru.nl>.

You can also access restricted access datasets via <https://webdav.data.donders.ru.nl>, this require registering, logging in and explicitly agreeing to the collection-specific Data Use Agreement (DUA). See [here](https://data.donders.ru.nl/doc/help/faq/access-shared-data.html) for instructions. The restricted access collections are not included here.

## Getting all collections as submodules

You can checkout this repository, which includes all data collections as submodules.

    git clone https://github.com/Donders-Institute-Data/donders-institute-data.git
    git submodule init
    git submodule update

Or you can combine these commands all at once with

    git clone --recurse-submodules https://github.com/Donders-Institute-Data/donders-institute-data.git

See the [submodules](https://git-scm.com/book/en/v2/Git-Tools-Submodules) documentation for details.

Subsequently you can download the specific files for a specific collection.

    cd <data…collection_id>
    datalad get 

See the [datalad handbook](http://handbook.datalad.org/en/latest/) for more details or `datalad --help`.
