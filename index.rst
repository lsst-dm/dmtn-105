..
  Technote content.

  See https://developer.lsst.io/restructuredtext/style.html
  for a guide to reStructuredText writing.

  Do not put the title, authors or other metadata in this document;
  those are automatically added.

  Use the following syntax for sections:

  Sections
  ========

  and

  Subsections
  -----------

  and

  Subsubsections
  ^^^^^^^^^^^^^^

  To add images, add the image file (png, svg or jpeg preferred) to the
  _static/ directory. The reST syntax for adding the image is

  .. figure:: /_static/filename.ext
     :name: fig-label

     Caption text.

   Run: ``make html`` and ``open _build/html/index.html`` to preview your work.
   See the README at https://github.com/lsst-sqre/lsst-technote-bootstrap or
   this repo's README for more info.

   Feel free to delete this instructional comment.

:tocdepth: 1

.. Please do not modify tocdepth; will be fixed when a new Sphinx theme is shipped.

.. sectnum::

.. TODO: Delete the note below before merging new content to the master branch.

.. note::

   **This technote is not yet published.**

Narrative summary of the Rubin Science Platform capabilities that will be available for prompt (near-real-time through few-day time scales) access to acquired data during the remainder of Construction (FY23-25) and, specifically, for AuxTel commissioning and main telescope commissioning.

Primarily motivated as a plan for the development of capabilities for the delivery of Prompt Products data, providing "practice" for the operation of the RSP for PP data in the first year of operations.
(Note that the Data Preview 0 activities are all essentially DRP-centric, and even DP1 does not provide for continuously updated data deliveries.)

One end point of the data access environment discussed in this document is the operational data-rights-holder access provided for Prompt Products at the beginning of the formal survey.
This will be summarized in a future expansion of RTN-011.

May also overlap somewhat with content in RTN-011 and other documents on the content and data access for early data releases (e.g., DP1) prior to the start of formal operations.

We will use the term "Prompt Products Preview" or "PP Preview" internally to this technote to refer to the set of capabilities and procedures it describes.
This isn't intended as a public-facing name -- as it is also not a public-facing service.

Background and Motivation
=========================

**Mission statement:** The principal aim of "Prompt Products Preview" is the commissioning of the mechanisms
required to make the Prompt Products available to science users once survey operations begin.

During LSST survey operations, a variety of image and catalog data products produced by the Alert Production system are provided to end users through the Rubin Science Platform on timescales from 24-80 hours.
This release process is comparable to annual data releases but must execute in an automated and continuous manner.
Several pre-operations exercises (DP0.1, DP0.2, and DP0.3) have provided opportunities to rehearse the annual data release process, but we have not yet developed the infrastructure and processes to support real-time release.

Alert Production and tests of alert distribution are expected to begin relatively soon after the System First Light milestone (see RTN-061).
It is therefore necessary to understand what Rubin Science Platform support will be provided for these data products at that time, and to develop and exercise the associated data release procedures now, prior to the beginning of ComCam and LSSTCam commissioning.
There are planned capabilities of the operations-era system with some complexity, such as the management of a 30-day cache of uncompressed (or losslessly compressed) PVIs and difference images from AP, and (possibly) the replacement of the PVIs with lossy-compressed versions.
We have to devise a system that works in the context of the transfer, around the 80-hour mark, of images from the "embargo" systems to the externally visible ones.
We also have to manage in a clean way the communication to users of the fact that, during early operations,
some observations will have templates available, and therefore yield difference images and DIA catalog data, while others will not.
We also must communicate the progressive increase in the proportion of sky covered by templates.

We would also like the system to meet some basic user-communication goals such as "where did LSSTCam look last night?", and the like.

The AuxTel LATISS imaging surveys provide a useful integration target for continuous release of data products produced by Alert Production.
Prompt Processing payloads are now running image processing pipelines on LATISS data in real-time, replicating the interfaces that will exist with LSSTCam at lower data volumes.

At this time there is no plan to release commissioning-era AuxTel imaging data or alerts publicly to science users or to alert brokers, so access to these products will be restricted to project members and SITCOM in-kind contributors.


Schedule
========

We are planning toward the following objectives:

AP operations
-------------

* First day running AP against Cassandra is attempted - mid-February 2024
* Successfully running AP against Cassandra on AuxTel data - mid-March 2024
* First day to attempt replicating Cassandra to Postgres - beginning of March 2024
* Stable running of AP on Cassandra on AuxTel data with replication to Postgres - end of March 2024

There is comfortable schedule margin available for these activities up through the start of May
if there are unexpected problems.

* Implementation of automated post-embargo sweeps to ``/repo/prompt`` - (TBD)
* Implementation of 30-day cache purges - TBC if this will even be done in early operations
  

Data services
-------------

* Exposure of ``/repo/embargo`` images via image services - prototype currently running
* Exposure of the expected user-facing image data products from ``/repo/prompt`` via image service - (TBD)
* Exposure of the PPDB tables from Postgres via TAP - April 2024 or as soon thereafter as the actual data are available

Is this the same Postgres server and the same TAP service as the one serving ``/repo/embargo`` ObsCore?

Notebook Aspect
---------------

* USDF RSP Nublado services are already in place
* Some work may still be needed on configuring appropriate "short names" for Prompt Products data sources
* Main effort will probably be to develop demonstration / tutorial notebooks to exercise
  realistic science-user data access use cases on the released data

Portal Aspect
-------------

* Basic Portal is available in USDF RSP now
* Configuration tweaks are needed soon to shift focus from DP0.2/DC2 universe to the real universe
* Refreshed Portal UI deployed on integration clusters by end of February, production by mid-April 2024
* "Simplified query" screens aimed at PP use cases - April 2024
  

Data Sources
============

To avoid the risk of rapid obsolescence, we avoid mentioning the dates of the various upcoming stages of
data acquisition and release here.
These dates are more authoritatively addressed in RTN-011.

AuxTel/LATISS
-------------

Initially the data available for use with "PP Preview" will be AuxTel/LATISS data taken on the Summit.

While it would be possible to do an AP-like processing of a simulated Rubin dataset, or a collection of imaging data from an ``afw``-compatible project such as DECam, the main point of the "PP Preview" is to exercise and demonstrate the ability to serve *progressively* the results of ongoing processing, so a one-time static processing of an existing data collection would not meet this need.

An observing run with AuxTel is currently done approximately every two weeks.
While over half of the time is devoted to spectroscopy, it appears that imaging data are still being taken on a regular basis.
Imaging and spectroscopic data may be separated readily on the filter ID, which has a distinctive "grism" value for the latter.

AuxTel imaging data is the primary initial target of "PP Preview".

ComCam
------

Since it now appears likely that the initial commissioning of the main telescope will use ComCam after all,
this will become a key target for "PP Preview" processing,
with a goal of having a suitable data flow from data acquisition through RSP access in place as early as
possible in the ComCam data acquisition era.

Delivery of AuxTel imaging data through "PP Preview" will continue into the ComCam era and will provide valuable
practice in working with multiple datasets.

While existing RSP tooling is usable for the display of a ComCam array's worth of images -- 9 CCDs -- at once,
that will not be the case for LSSTCam.
Therefore, one aim of the ComCam era of "PP Preview" should be to exercise tooling for visualization of
focal-plane arrays of data in a way that *will* scale to LSSTCam, even though it may not be strictly
needed on this time scale.
This is briefly discussed further below, but the planned work in this area is yet to be defined in detail.

LSSTCam
-------

Once commissioning switches to LSSTCam, we anticipate continuing "PP Preview" service as soon as possible.
This will depend on successfully scaling up all the processing and data service elements to the new load.
Commissioning a user-facing full-focal-plane display capability will be a key goal of this period in
addition to all of the pure scaling issues that will be involved.

As noted elsewhere, "PP Preview" service of LSSTCam to staff is in addition to and in parallel with
any Data Preview 1 service of a limited set of commissioning data to users.


Data Processing and Data Products
=================================

The aim is to always be in the position to perform some level of automated, AP-like, processing of all imaging data taken, regardless of whether templates are available or not for the section of sky being observed.
This processing will, at a minimum, always produce a PVI/``calexp`` for each detector image.

If templates are available, the processing will also produce a difference image,
and associated DIA catalog content (``DiaSource``, ``DiaObject``, etc.) that begins in the APDB and is migrated to the PPDB for user access.
(In early versions of this, before the APDB migrates to Cassandra, there will be no separate PPDB,
and "PP Preview" catalog data might be served directly from the APDB.)

The ability to do this data processing in an automated way, at LATISS scale, has already been exercised in recent work.

When template images become available for use in the AP pipeline, the logic of consistency and availability
of provenance suggests that they must also become available via "PP Preview".


Source-like Data
----------------

As part of its normal operation, the AP pipeline payload also produces a ``src`` table in ``afw.table`` FITS-file form in the Butler repository.
These outputs are not closely equivalent to the DRP ``Source`` outputs,
either in source selection or in computed attributes,
as they are principally generated to support the calibration of the data.

*(Add text from EB summarizing what's different?)*

In the original LSST plan these outputs were not planned to be made available to science users.
However, there are now ongoing discussions about whether the content of the ``src`` outputs should be
made available to users in some way, during the first year of operations, before DR1 is available
(one year following the start of survey operations).
Otherwise, users would not receive any catalog-like data on the static sky before that time.
The long-term baseline, however, remains that they will ultimately *not* be available to science users.

As the project's thinking about this option evolves, we will want to take this into account in "PP Preview".

If ``src``-like data are made available to science users, the following constraints will likely apply:

* They will be a subset both in rows and columns of what the full ``Source`` table will contain in a Data Release.
* They will be provided in a Parquet form following the application of some level of "SDM Standardization"-like processing.
* They will be accessed via the Notebook Aspect as a Butler dataset, with the same DataID of a CCD visit image.

Other considerations will be discussed below.


Spectroscopic Data
------------------

At some point the AuxTel/LATISS spectroscopic data may be run through an AP-like pipeline,
up through the ISR stage,
and these results would be readily made available through the "PP Preview" system as well.
Whether this will actually be done is TBD, and whether the actual spectroscopic reduction outputs, or
any resulting atmospheric model updates, would be available via "PP Preview" remains to be determined as well.


Data Access
===========

This section covers the ways in which data from the processing above would be made available to the
(internal) RSP users of "PP Preview".

Overview
--------

The AuxTel and subsequent processed datasets described above are meant to be made available in the RSP
in a way which progressively
approaches exactly how LSSTCam Prompt Products will be made available to science users during operations.
This will follow the Science Platform Vision's model where data are available as uniformly as
possible across the API, Notebook, and Portal Aspects.

Butler Access
-------------

What may evolve over the course of "PP Preview" is that early on access may be only through
the ``/repo/embargo`` repository, while by the end of "PP Preview" we'll have to have the
full "release from embargo to (staff) users at 80 hours" working, and therefore have the Butler
that represents the latter dataset, ``/repo/prompt``, available in the RSP.


RSP Data Services (API Aspect)
------------------------------


Image Data
^^^^^^^^^^

As in DP0.2, image data access through the API Aspect depends on several elements:

* ObsCore image metadata queries via TAP
* A DataLink "links service" endpoint to connect image dataset IDs from ObsCore queries
* Image-retrieval signed URLs for the actual image access, returned from the "links service"
* An image-cutout service pointed to by metadata from the "links service"


CAOM2 metadata
""""""""""""""

It's not a primary goal of PP Preview to exercise the creation and provision of CAOM2 metadata for
acquired data, but it would be a useful stretch goal.

Architecture-team guidance for how this metadata will be created and stored in a database will
be required in order for progress to be made in this area.

Catalog Data
^^^^^^^^^^^^

The final version of PP Preview will have to demonstrate the ability to migrate data from
the APDB to the PPDB.
The PPDB is expected to be a Postgres database.
It is not completely clear whether it's the same database as will be used for the image metadata.

RSP Portal Aspect
-----------------

As in DP0.2 and as as planned for ops, the Portal Aspect obtains data through a combination
of direct access to IVOA-standard interfaces, mostly importantly TAP queries and DataLink links services,
and through DataLink-compatible but not specifically standardized data services discovered via DataLink.

Image Data
^^^^^^^^^^

The Portal for PP Preview will access image data from TAP queries to ObsCore-formatted tables.

The equivalent of data-rights-user access to post-embargo data will be via a true "ObsTAP" service,
with the table named ``ivoa.ObsCore``.
That table will represent the image data in the post-embargo Butler repository.

Early access to pre-release data will be via tables representing ``/repo/embargo`` or any other Butler
repositories that are relevant.
Users will be able to select these tables in the PP Preview Portal's TAP screen, and when they do so,
as is already true for the prototype ``live ObsCore`` service, the Portal will display the same
search screen that it does for true "ObsTAP" services.

If and when CAOM2 metadata becomes available, it will be searchable via TAP in the Portal Aspect, but the
PP Preview era Portal will not provide any dedicated CAOM2-based search capabilities.

Catalog Data
^^^^^^^^^^^^

As noted above, toward the end of PP Preview the DIA catalog products will be replicated to the Postgres PPDB.
Portal access to that data will simply be via TAP in a way that has largely been exercised in DP0.2 and DP0.3.

As notec abov, what is new here is the evolving nature of the ``DiaObject`` table, in particular.
While that's not *per se* a problem for the Portal Aspect, we know that there are both user expectations
and actual requirements (*vide supra*) on the reproducibility of queries.

(TBS: list of requirements)

PP Preview will allow these use cases to be exercised in the Portal Aspect, and will help us to
determine whether we need a dedicated UI capability to support capturing PPDB queries in a
reproducible form.

RSP Notebook Aspect
-------------------

There are no special considerations for the Notebook Aspect beyond the above,
but we summarize here the implications for Notebook users and highlight the difference
between using the PP Preview environment simply for the staff's own data access needs,
as opposed to a rehearsal of ops-era science-user data access.

The principal path to image data access, in PP Preview as well as in operations,
will be via access to a Butler.

As in DP0.2, it will also be possible to invoke the IVOA image services from a Notebook,
with the same mechanism we now have for making it easy for users to supply appropriate
authentication tokens to those service invocations.
This may be the most convenient path for users to invoke some of the image-adjacent
services that will be advertised via the "links service" in our image data service
model.

Initially, DIA catalog data will be accessible via Butler, which is useful for staff-facing QA work,
but is not a realistic representation of the ops-era data access environment.
Also note that Butler access to ``DiaObject`` is essentially not a science-friendly solution,
as it is cumulatively very difficult to assemble a coherent updated set of ``DiaObjects``.

So, ultimately, use will have to evolve toward accessing the DIA catalog products in the
Notebook Aspect via TAP queries.


Appendix: PPDB Query Reproducibility
------------------------------------

The ideal scenario from a user perspective would be to be able to perform queries against the PPDB
in a "natural" way, without explicitly including DiaObject-versioning constraints in their original query,
and to have the system respond to such a query not only with the query results but also with information
that could be used to repeat the query exactly.

The PPDB design envisions this core of this information to be a timestamp to be compared to the
``DiaObject`` update times,
but from a user perspective very precise instructions for how to implement this in ADQL are also needed.
This is particularly important for queries originally constructed in a way which does not directly involve
the user in seeing the ADQL text.
These could be queries from the Portal's graphical query-builders or from Python libraries that
simplify query generation, as is done in ``astroquery`` (which I assume we'll be supporting for Rubin use).

The IVOA standards we use do provide a way to do this:
a query result from TAP for the PPDB could come back with metadata in a number of different places in
the VOTable that could provide the above functionality.
For instance, a tabular query result could contain a service descriptor providing a means to repeat
the query with the necessary modification to the ADQL to make it stable against future DiaObject updates.

This "ideal scenario" approach is probably the only solution to the problem that has the ability to have
exact reproducibility --
something amounting to transaction semantics / atomicity is required in order to capture
the precise timestamp needed to repeat a query.

Reproducible PPDB queries -- the ADQL details
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^


Supporting reproducible queries in TAP
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. Add content here.
.. Do not include the document title (it's automatically added from metadata.yaml).

.. .. rubric:: References

.. Make in-text citations with: :cite:`bibkey`.

.. .. bibliography:: local.bib lsstbib/books.bib lsstbib/lsst.bib lsstbib/lsst-dm.bib lsstbib/refs.bib lsstbib/refs_ads.bib
..    :style: lsst_aa
