.. Tip - just do it:
      don't use TABs (= \t, tabulators)
      replace each TAB by *three blanks* (enable RegExp for Search and Replace in your IDE)
      set TAB width and indentation to THREE in your IDE
      set 'Use blanks instead of TABs' in your IDE


.. With the following include we import some definition. We do this in each and every file.
   so we can change the definition at a single place. Use the relative path to the Includes.txt file,
   which may look as well like ../../../Includes.txt for a deeply nested source file.

.. include:: Includes.txt


.. Usually we define 'php' as default highlight language in Includes.txt.
   With the following 'highlight' directive we switch to reStructuredText as default highlight language.

.. highlight:: rst


.. The following, first section (= headline) is the 'Document Title'.


=========================
Using Composer With TYPO3
=========================


.. The following is 'field list' which is rendered as a horizontal table.
   Think of it as key-value pairs.


:Writing here:    several
:Rendered:        |today|
:Buildinfo:       `buildinfo <_buildinfo>`_
:Others:          `Who else has a starter project?`__

__ https://docs.typo3.org/typo3cms/drafts/github/T3DocumentationStarter/Public-Info-001/TeachingDocs/StarterManuals/

.. tip::

   **to alexanderschnitzler**, **helhum**, **NamelessCoder**:

   - You are admin of this Github repository
   - Accept the Github invitation
   - You can write here as easy as writing in a Gist
   - Please do so!

   **No cloning, no fork, no commit message required.** 
   Just "Edit me on Github", write, save.




.. **Contents:**

.. toctree::
   :hidden:
   
   AboutComposer/Index
   Incoming/Index
   
