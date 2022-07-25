Skill Tagging of Programs
=========================================================

Status
------

In Progress

Context
-------
Programs in course-discovery do not have a direct way to getting tagged with Taxonomy skills. Instead,
the determination the skills that a program would offer as the outcomes is performed using the linked courses.
The course skills information is present in `taxonomy-connector models`_. By listing and aggregating all the skills
under the courses, the skills of a program are determined. However, with the import of external programs (degrees) where
there are no associated courses, the skills of a program can not be determined. With no relevant skill tags available,
the external programs can not be effectively used in Algolia search.

.. _taxonomy-connector models: https://github.com/openedx/taxonomy-connector/blob/09bc066ae66ed4bea73f70811dedc0853e2fe077/taxonomy/models.py#L102

Decision
--------
A new model called ProgramSkills will be added in `taxonomy-connector`_, using the same approach as CourseSkill model.
This model will be responsible for containing the information of skills associated with a Program. The tagging process
for new programs will work as follows:

1. When a program is published in Discovery, emit a signal indicating the program skills must be updated.
2. In taxonomy-connector, add a `signal handler`_ that is listening to the event published by course-discovery.
3. Upon receiving the signal, get Program information from Discovery and send "overview" field contents to EMSI via EMSI API.
4. With a successful EMSI API call, create or update the skill information for Program in taxonomy app.

The above mentioned process can work for any type of Program (with or without associated courses). To back-populate the Programs
that are already present in the Catalog, a management command that is performing the above steps in a similar capacity would be needed.

.. _taxonomy-connector: https://github.com/openedx/taxonomy-connector/blob/09bc066ae66ed4bea73f70811dedc0853e2fe077/taxonomy/models.py
.. _signal handler: https://github.com/openedx/taxonomy-connector/tree/09bc066ae66ed4bea73f70811dedc0853e2fe077/taxonomy/signals

Consequences
------------

* The program skill information would need to be indexed in Algolia.
