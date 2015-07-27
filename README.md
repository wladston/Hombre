Hombre: Homebrew Movie Repo
===========================

This project is a first attempt to empower you to have your own homebrew movie
repository that you can use most effectively. The iMBD's dataset is incredibly
complete, but it's API can be a pain to use, and the data formats they use to
distribute their data are (literally) from decades ago. There is also tMDB,
that you can also fetch for free, but according to their lengthy user license,
you can't even keep a cached copy in your machine.

We have a lot of information on DBPedia, FreeBase and LinkedMDB, but each one
of these services uses a different vocabulary, and it's very hard to integrate
information from all this data.

Enter Hombrew: the Homebrew Movie Repo. With it, you can create a JSON-LD
representation of existing movie data sources (for now, IMDb and Freebase are
supported), and integrate them right away with whatever it is you need. If you
like the idea, you too can create a JSON-LD parser for your favourite movie
source using the schema.org vocabulary, and submit a pull request.

How to use
==========
First, make sure you have Python 2.7 installed with all the packages in
`requireents.txt` installed to your current environment.


## Extracting iMBD data
1. Create a directory to hold your iMBD stuff, and download the imdbpy2sql.py script
from Github in it. Start downloading the iMDB files, it's about 1.5GB large.

    $ pip install -r requirements.txt
    $ mkdir temp && cd temp
    $ wget https://raw.githubusercontent.com/alberanid/imdbpy/master/bin/imdbpy2sql.py
    $ wget -r --accept="*.gz" --no-directories --no-host-directories --level 1 ftp://ftp.fu-berlin.de/pub/misc/movies/database/


2. Once you have all the files, generate the sqlite database. That should take
   about 45 minutes using an SSD disk.  Make sure that the imdbpy package is
   installed in your environment.

    $ python imdbpy2sql.py -u sqlite:./imdb.sqlite -d temp.gz --sqlite-transactions

3. With your fresh `imdb.sqlite`, get some indexes in it, because this database is 
quite large and we'll query these fields all the time.

    $ sqlite3 imdb.sqlite
    sqlite> CREATE INDEX title_idx_kind_id on title (kind_id);
    sqlite> CREATE INDEX movie_info_idx_itid on movie_info_idx (movie_id, info_type_id);

4. You're all set! Check the `parser_imdb.py` script to make sure it can find
   your `imdb.sqlite`, and run the script. It will output the most popular
   movies in the JSON-LD format. You can pipe the script to a file and let it
   run overnight (because boy, this takes a while! 😴  😩  😡 )


## Extracting LinkedMDB data
Run the script and you'll see it grab triples from LinkedMDB right away from
the internet, requesting a conversion to the schema.org vocabulary on the fly.
(Vive le Open Data 😎 ). The problem is, for not, it looks like their sparql
endpoint won't manage to let us get a dump all at once without crashing.

## The Future
Most importantly, we have to fix the LinkedMDB script to get data in bulk, and
then integrate the dataset from the two services, possibly using MongoDB to
keep things simple and fast. Later on the sky is the limit, we can create parsers
for every dataset out there.

## License
© 2015/07 Wladston Viana (wladston@wladston.net) under the BSD License.
