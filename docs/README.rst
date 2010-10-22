
=============================
DDBC Authority Time Database
=============================

:Author: Simon Wiles, DDBC
:Contact: digital_archives@ddbc.edu.tw
:Web site: http://authority.ddbc.edu.tw/
:Date: Oct, 2010

.. contents::


Schema Diagram
==============

.. figure:: /ddbc/Authority-Databases/raw/master/docs/authority_time.png
   :scale: 100
   :align: left
   :alt: DDBC Authority Time Database - schema diagram
   :figwidth: 100%

   This image above shows the schema for the Database as form of Entity
   Relation Diagram (ERD).



Explanation
===========

The database contains four major entities: **dynasty**, **emperor**, **era** and
**month**.

A **dynasty** entity represents a dynasty (朝代) in Chinese history, or a period
(時代) in Japanese or Korean history. Each **dynasty** has one or more
*dynasty_names* and none or more *dynasty_comments*. A dynasty will usually
encompass the reigns of several different emperors (皇帝). Each **emperor** entity
will also have one or more *emperor_names* and none or more *emperor_comments*,
and is linked to its parent **dynasty** by a Foreign Key relation. Each emperor's
reign consists of one or more eras (年號), represented as **era** entities in the
database. An **era** entity has *era_names* and may have *era_comments*, and is
linked to its parent **emperor**. Finally, each **era** consists of one or more
**month** entities, which stand for lunar months.  A **month** entity has a Foreign
Key dependency on an **era**, and may have *month_comments*.

There is also a *day_comments* table which is indexed by Julian Day Numbers, and
a *by_year* VIEW, which facilitates easier querying on a year-by-year basis.

Finally, there is a *period* table, which contains references to time periods
which do not fit into the schema outlined above.  A *period* may or may not be
linked directly to a **dynasty**, in which case it gives the actual historical
extent of that dynasty, and must be unique.

In the DDBC Time Authority Database, the lunar month is the smallest unit of
data: i.e. the database does not store a record for each day. Nevertheless, the
details of an individual day can be derived from the corresponding **month**
entity.

See the comments to each table and field below for more information.


Tables
======

c_languages
-----------

+----------------+-------------+-------------+-------+-------+---------+---------+
| Name           | Foreign Key | Data-type   | PK?   | NULL? | Default | Comment |
+================+=============+=============+=======+=======+=========+=========+
| id             |             | tinyint(3)  | true  | false |         |         |
+----------------+-------------+-------------+-------+-------+---------+---------+
| code           |             | varchar(8)  | false | false |         |         |
+----------------+-------------+-------------+-------+-------+---------+---------+
| long_name_zho  |             | varchar(20) | false | false |         |         |
+----------------+-------------+-------------+-------+-------+---------+---------+
| short_name_zho |             | varchar(10) | false | false |         |         |
+----------------+-------------+-------------+-------+-------+---------+---------+
| long_name_eng  |             | varchar(30) | false | false |         |         |
+----------------+-------------+-------------+-------+-------+---------+---------+
| short_name_eng |             | varchar(10) | false | false |         |         |
+----------------+-------------+-------------+-------+-------+---------+---------+

t_by_year
---------

+------------+-------------+--------------+-------+-------+---------+---------+
| Name       | Foreign Key | Data-type    | PK?   | NULL? | Default | Comment |
+============+=============+==============+=======+=======+=========+=========+
| era_id     |             | smallint(5)  | false | false |         |         |
+------------+-------------+--------------+-------+-------+---------+---------+
| yearGanzhi |             | char(6),     | false | false |         |         |
+------------+-------------+--------------+-------+-------+---------+---------+
| first      |             | mediumint(8) | false | false |         |         |
+------------+-------------+--------------+-------+-------+---------+---------+
| last       |             | mediumint(8) | false | false |         |         |
+------------+-------------+--------------+-------+-------+---------+---------+

t_day_comments
--------------

+---------+-------------+--------------+-------+-------+---------+---------+
| Name    | Foreign Key | Data-type    | PK?   | NULL? | Default | Comment |
+=========+=============+==============+=======+=======+=========+=========+
| id      |             | smallint(5)  | true  | false |         |         |
+---------+-------------+--------------+-------+-------+---------+---------+
| jd      |             | mediumint(8) | false | false |         |         |
+---------+-------------+--------------+-------+-------+---------+---------+
| comment |             | text         | false | false |         |         |
+---------+-------------+--------------+-------+-------+---------+---------+

t_dynasty
---------

+------+-------------+-------------------------------------+-------+-------+---------+---------+
| Name | Foreign Key | Data-type                           | PK?   | NULL? | Default | Comment |
+======+=============+=====================================+=======+=======+=========+=========+
| id   |             | smallint(5)                         | true  | false |         |         |
+------+-------------+-------------------------------------+-------+-------+---------+---------+
| type |             | enum('chinese','japanese','korean') | false | false |         |         |
+------+-------------+-------------------------------------+-------+-------+---------+---------+

t_dynasty_comments
------------------

+------------+--------------+-------------+-------+-------+---------+---------+
| Name       | Foreign Key  | Data-type   | PK?   | NULL? | Default | Comment |
+============+==============+=============+=======+=======+=========+=========+
| id         |              | smallint(5) | true  | false |         |         |
+------------+--------------+-------------+-------+-------+---------+---------+
| comment    |              | text        | false | false |         |         |
+------------+--------------+-------------+-------+-------+---------+---------+
| dynasty_id | t_dynasty.id | smallint(5) | false | false |         |         |
+------------+--------------+-------------+-------+-------+---------+---------+

t_dynasty_names
---------------

+-------------+----------------+-------------+-------+-------+---------+---------+
| Name        | Foreign Key    | Data-type   | PK?   | NULL? | Default | Comment |
+=============+================+=============+=======+=======+=========+=========+
| dynasty_id  | t_dynasty.id   | smallint(5) | true  | false | 0       |         |
+-------------+----------------+-------------+-------+-------+---------+---------+
| name        |                | varchar(10) | false | false |         |         |
+-------------+----------------+-------------+-------+-------+---------+---------+
| ranking     |                | tinyint(3)  | true  | false | 0       |         |
+-------------+----------------+-------------+-------+-------+---------+---------+
| language_id | c_languages.id | tinyint(3)  | false | false | 0       |         |
+-------------+----------------+-------------+-------+-------+---------+---------+

t_emperor
---------

+------------+--------------+-------------+-------+-------+---------+---------+
| Name       | Foreign Key  | Data-type   | PK?   | NULL? | Default | Comment |
+============+==============+=============+=======+=======+=========+=========+
| id         |              | smallint(5) | true  | false |         |         |
+------------+--------------+-------------+-------+-------+---------+---------+
| dynasty_id | t_dynasty.id | smallint(5) | false | false | 0       |         |
+------------+--------------+-------------+-------+-------+---------+---------+

t_emperor_comments
------------------

+------------+--------------+-------------+-------+-------+---------+---------+
| Name       | Foreign Key  | Data-type   | PK?   | NULL? | Default | Comment |
+============+==============+=============+=======+=======+=========+=========+
| id         | t_emperor.id | smallint(5) | true  | false |         |         |
+------------+--------------+-------------+-------+-------+---------+---------+
| emperor_id |              | smallint(5) | false | false |         |         |
+------------+--------------+-------------+-------+-------+---------+---------+
| comment    |              | text        | false | false |         |         |
+------------+--------------+-------------+-------+-------+---------+---------+

t_emperor_names
---------------

+-------------+----------------+-------------+-------+-------+---------+---------+
| Name        | Foreign Key    | Data-type   | PK?   | NULL? | Default | Comment |
+=============+================+=============+=======+=======+=========+=========+
| emperor_id  | t_emperor.id   | smallint(5) | true  | false | 0       |         |
+-------------+----------------+-------------+-------+-------+---------+---------+
| name        |                | varchar(10) | false | false |         |         |
+-------------+----------------+-------------+-------+-------+---------+---------+
| ranking     |                | tinyint(3)  | true  | false | 0       |         |
+-------------+----------------+-------------+-------+-------+---------+---------+
| language_id | c_languages.id | tinyint(3)  | false | false | 0       |         |
+-------------+----------------+-------------+-------+-------+---------+---------+

t_era
-----

+------------+--------------+-------------+-------+-------+---------+---------+
| Name       | Foreign Key  | Data-type   | PK?   | NULL? | Default | Comment |
+============+==============+=============+=======+=======+=========+=========+
| id         |              | smallint(5) | true  | false |         |         |
+------------+--------------+-------------+-------+-------+---------+---------+
| emperor_id | t_emperor.id | smallint(5) | false | false |         |         |
+------------+--------------+-------------+-------+-------+---------+---------+

t_era_comments
--------------

+---------+-------------+-------------+-------+-------+---------+---------+
| Name    | Foreign Key | Data-type   | PK?   | NULL? | Default | Comment |
+=========+=============+=============+=======+=======+=========+=========+
| id      |             | smallint(5) | true  | false |         |         |
+---------+-------------+-------------+-------+-------+---------+---------+
| era_id  | t_era.id    | smallint(5) | false | false |         |         |
+---------+-------------+-------------+-------+-------+---------+---------+
| comment |             | text        | false | false |         |         |
+---------+-------------+-------------+-------+-------+---------+---------+

t_era_names
-----------

+-------------+----------------+-------------+-------+-------+---------+---------+
| Name        | Foreign Key    | Data-type   | PK?   | NULL? | Default | Comment |
+=============+================+=============+=======+=======+=========+=========+
| era_id      | t_era.id       | smallint(5) | true  | false | 0       |         |
+-------------+----------------+-------------+-------+-------+---------+---------+
| name        |                | varchar(10) | false | false |         |         |
+-------------+----------------+-------------+-------+-------+---------+---------+
| ranking     |                | tinyint(3)  | true  | false | 0       |         |
+-------------+----------------+-------------+-------+-------+---------+---------+
| language_id | c_languages.id | tinyint(3)  | false | false | 0       |         |
+-------------+----------------+-------------+-------+-------+---------+---------+

t_month
-------

+------------+-------------+---------------+-------+-------+---------+----------------------------------------+
| Name       | Foreign Key | Data-type     | PK?   | NULL? | Default | Comment                                |
+============+=============+===============+=======+=======+=========+========================================+
| id         |             | mediumint(8)  | true  | false |         |                                        |
+------------+-------------+---------------+-------+-------+---------+----------------------------------------+
| year       |             | tinyint(3)    | false | false |         | Ordinal Year of Era                    |
+------------+-------------+---------------+-------+-------+---------+----------------------------------------+
| month      |             | tinyint(3)    | false | false |         |                                        |
+------------+-------------+---------------+-------+-------+---------+----------------------------------------+
| month_name |             | varchar(3)    | false | false |         |                                        |
+------------+-------------+---------------+-------+-------+---------+----------------------------------------+
| leap_month |             | tinyint(1)    | false | false | 0       |                                        |
+------------+-------------+---------------+-------+-------+---------+----------------------------------------+
| era_id     | t_era.id    | smallint(5)   | false | false |         |                                        |
+------------+-------------+---------------+-------+-------+---------+----------------------------------------+
| first      |             | mediumint(8)  | false | false |         |                                        |
+------------+-------------+---------------+-------+-------+---------+----------------------------------------+
| last       |             | mediumint(8)  | false | false |         |                                        |
+------------+-------------+---------------+-------+-------+---------+----------------------------------------+
| ganzhi     |             | char(6)       | false | false |         |                                        |
+------------+-------------+---------------+-------+-------+---------+----------------------------------------+
| start_from |             | tinyint(3)    | false | false | 1       | First Day of Month is which ordinal?   |
+------------+-------------+---------------+-------+-------+---------+----------------------------------------+
| status     |             | enum('S','P') | false | false | S       |                                        |
+------------+-------------+---------------+-------+-------+---------+----------------------------------------+
| eclipse    |             | tinyint(3)    | false | false | 0       | First Day of Month is a Solar Eclipse? |
+------------+-------------+---------------+-------+-------+---------+----------------------------------------+

t_month_comments
----------------

+----------+-------------+--------------+-------+-------+---------+---------+
| Name     | Foreign Key | Data-type    | PK?   | NULL? | Default | Comment |
+==========+=============+==============+=======+=======+=========+=========+
| id       |             | smallint(5)  | true  | false |         |         |
+----------+-------------+--------------+-------+-------+---------+---------+
| month_id | t_month.id  | mediumint(8) | false | false |         |         |
+----------+-------------+--------------+-------+-------+---------+---------+
| comment  |             | text         | false | false |         |         |
+----------+-------------+--------------+-------+-------+---------+---------+

t_period
--------

+-------------+--------------+--------------+-------+-------+---------+---------+
| Name        | Foreign Key  | Data-type    | PK?   | NULL? | Default | Comment |
+=============+==============+==============+=======+=======+=========+=========+
| id          |              | smallint(5)  | true  | false | 0       |         |
+-------------+--------------+--------------+-------+-------+---------+---------+
| dynasty_id  | t_dynasty.id | smallint(5)  | false | true  | NULL    |         |
+-------------+--------------+--------------+-------+-------+---------+---------+
| first       |              | mediumint(8) | false | false |         |         |
+-------------+--------------+--------------+-------+-------+---------+---------+
| last        |              | mediumint(8) | false | false |         |         |
+-------------+--------------+--------------+-------+-------+---------+---------+
| description |              | varchar(30)  | false | false |         |         |
+-------------+--------------+--------------+-------+-------+---------+---------+
| note        |              | text         | false | false |         |         |
+-------------+--------------+--------------+-------+-------+---------+---------+
