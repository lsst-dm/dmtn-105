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

During LSST survey operations, a variety of image and catalog data products produced by the Alert Production system are provided to end users through the Rubin Science Platform on timescales from 24-80 hours.
This release process is comparable to annual data releases but must execute in an automated and continuous manner.
Several pre-operations exercises (DP0.1, DP0.2, and DP0.3) have provided opportunities to rehearse the annual data release process, but we have not yet developed the infrastructure and processes to support real-time release.

Alert Production and alert distribution are expected to begin relatively soon after the System First Light milestone (see RTN-061).
It is therefore necessary to understand what Rubin Science Platform support will be provided for these data products at that time, and to develop and exercise the associated data release procedures now, prior to the beginning of LSSTCam commissioning.
There are planned capabilities with some complexity, such as the management of a 30-day cache of uncompressed (or losslessly compressed) PVIs and difference images from AP, and (possibly) the replacement of the PVIs with lossy-compressed versions.
We have to devise a system that works in the context of the transfer, around the 80-hour mark, of images from the "embargo" systems to the externally visible ones.
We also have to manage in a clean way the communication to users of the fact that some observations have templates available, and therefore yield difference images and DIA catalog data, while others will not, as well as the progressive increase in the proportion of sky covered by templates.

We would also like to exercise some basic user-communication goals such as "where did LSSTCam look last night?", and the like.

The AuxTel LATISS imaging surveys provide a useful integration target for continuous release of data products produced by Alert Production.
Prompt Processing payloads are now running image processing pipelines on LATISS data in real-time, replicating the interfaces that will exist with LSSTCam at lower data volumes.

At this time there is no plan to release commissioning-era AuxTel imaging data or alerts publicly to science users or to alert brokers, so access to these products will be restricted to project members and SITCOM in-kind contributors.


Data Sources and Datasets
=========================

Initially the data available for use with "PP Preview" will be AuxTel/LATISS data taken on the Summit.

While it would be possible to do an AP-like processing of a simulated Rubin dataset, or a collection of imaging data from an ``afw``-compatible project such as DECam, the main point of the "PP Preview" is to exercise and demonstrate the ability to serve *progressively* the results of ongoing processing, so a one-time static processing of an existing data collection would not meet this need.

An observing run with AuxTel is currently done approximately every two weeks.
While over half of the time is devoted to spectroscopy, it appears that imaging data are still being taken on a regular basis.
Imaging and spectroscopic data may be separated readily on the filter ID, which has a distinctive "grism" value for the latter.

The aim is to shortly be in the position to perform some level of automated, AP-like, processing of all imaging data taken, regardless of whether templates are available or not for the section of sky being observed.
This processing will always produce a PVI/``calexp``, as well as a ``src`` table (in ``afw.table`` FITS-file form in the Butler repository).
The ``src`` table will almost certainly not be ingested into a database.
Note that there are ongoing discussions about whether the ``src`` outputs should be made available to users, during the first year of operations, before DR1 is available; the long-term baseline, however, remains that they will ultimately *not* be available to science users.

If templates are available, the processing will also produce a difference image,
and associated DIA catalog content (``DiaSource``, ``DiaObject``, etc.) that begins in the APDB and is migrated to the PPDB for user access.

The ability to do all the above in an automated way has been deployed in Summer 2023 work.

Note that at some point the spectroscopic data may be run through an AP-like pipeline, up through the ISR stage, and these results would be readily made available through the "PP Preview" system as well.

Whether this would be applied to the actual spectroscopic reductions,
or to any resulting atmospheric model updates, remains to be determined.


Data Access
===========

This section covers the ways in which data from the processing above would be made available to the
(internal) RSP users of "PP Preview".

Overview
--------

The AuxTel processing above is meant to be made available in the RSP in a way which progressively
approaches exactly how LSSTCam Prompt Products will be made available to science users during operations:
we will follow the model where data are available as uniformly as possible across the API, Notebook,
and Portal Aspects.

Butler Access
-------------

What may evolve over the course of "PP Preview" is that early on access may be only through
the ``/repo/embargo`` repository, while by the end of "PP Preview" we'll have to have the
full "release from embargo to users at 80 hours" working, and therefore have a Butler
available that represents the latter dataset.


RSP Data Services
-----------------


Image Data
^^^^^^^^^^

In addition to the TAP access to ObsCore image metadata,

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
