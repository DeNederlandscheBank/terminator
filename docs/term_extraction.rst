Term extraction
===============

Create a termbase from extracted terms
--------------------------------------

We generate an empty TBX document with

::

    termbase = termate.TbxDocument()
    termbase.generate(params = {
        termate.TBX_DIALECT: "TBX-DNB",
        termate.TBX_STYLE: "dca",
        termate.TBX_RELAXNG: "https://github.com/DeNederlandscheBank/termate/blob/main/data/dialects/TBX-DNB.rng",
        termate.SOURCEDESC: ["TBX file, created via dnb/termate"],
        termate.TITLE: ["Example termbase"],
        termate.PUBLICATION: ["Created on ..."]
    })

Then we extract terms from the Solvency II Delegated Acts (Dutch version) in NAF:

::

    # create terms dictionary of subset of languages
    terms = {}
    for language in ['NL', 'EN', 'DE', 'FR', 'ES', 'ET', 'DA', 'SV']:
        DOC_FILE = "..\\..\\nafigator-data\\data\\legislation\\Solvency II Delegated Acts - "+language+".naf.xml"
        doc = nafigator.NafDocument().open(DOC_FILE)
        termate.merge_terms_dict(terms, nafigator.extract_terms(doc))

Then we create a termbase

::

    # add concepts from a dictionary of terms
    termbase.create_tbx_from_terms_dict(terms=terms, 
                                 params={'concept_id_prefix': 'tbx_'})

Then we add references from the InterActive Terminology for Europe (IATE) dataset:

::

    # read the IATE file
    IATE_FILE = "..//data//iate//IATE_export.tbx"
    ref = termate.TbxDocument().open(IATE_FILE)
    termbase.copy_from_tbx(reference=ref)

Then we add termnotes from the Dutch Lassy dataset (the small one) including basic insurance terms:

::

    # read the lassy file
    LASSY_FILE = "..//data//lassy//lassy_with_insurance.tbx"
    lassy = termate.TbxDocument().open(LASSY_FILE)
    termbase.add_termnotes_from_tbx(reference=lassy, params={'number_of_word_components':  5})

Then we have a termbase with:

::

    <conceptEntry id="249">
     <descrip type="subjectField">insurance</descrip>
     <xref>IATE_2246604</xref>
     <ref>https://iate.europa.eu/entry/result/2246604/en</ref>
     <langSec xml:lang="nl">
      <termSec>
       <term>solvabiliteitskapitaalvereiste</term>
       <termNote type="partOfSpeech">noun</termNote>
       <note>source: data/Solvency II Delegated Acts - NL.txt (#hits=331)</note>
       <termNote type="termType">fullForm</termNote>
       <descrip type="reliabilityCode">9</descrip>
       <termNote type="lemma">solvabiliteits_kapitaalvereiste</termNote>
       <termNote type="grammaticalNumber">singular</termNote>
       <termNoteGrp>
        <termNote type="component">solvabiliteits-</termNote>
        <termNote type="component">kapitaal-</termNote>
        <termNote type="component">vereiste</termNote>
       </termNoteGrp>
      </termSec>
     </langSec>
     <langSec xml:lang="en">
      <termSec>
       <term>SCR</term>
       <termNote type="termType">abbreviation</termNote>
       <descrip type="reliabilityCode">9</descrip>
      </termSec>
      <termSec>
       <term>solvency capital requirement</term>
       <termNote type="termType">fullForm</termNote>
       <descrip type="reliabilityCode">9</descrip>
       <termNote type="partOfSpeech">noun, noun, noun</termNote>
       <note>source: data/Solvency II Delegated Acts - EN.txt (#hits=266)</note>
      </termSec>
     </langSec>
     <langSec xml:lang="fr">
      <termSec>
       <term>capital de solvabilité requis</term>
       <termNote type="termType">fullForm</termNote>
       <descrip type="reliabilityCode">9</descrip>
       <termNote type="partOfSpeech">noun, adp, noun, adj</termNote>
       <note>source: ../nafigator-data/data/legislation/Solvency II Delegated Acts - FR.txt (#hits=198)</note>
      </termSec>
      <termSec>
       <term>CSR</term>
       <termNote type="termType">abbreviation</termNote>
       <descrip type="reliabilityCode">9</descrip>
      </termSec>
     </langSec>
    </conceptEntry>

* a reference is included to concept '2246604' from the IATE dataset. From that reference, we can for example derive that the official European term for this concept in English is 'solvency capital requirement' and in German 'Solvenzkapitalanforderung' and that the term is defined in Directive 2009/138/EC (Solvency II).

* termNotes include the partOfSpeech, lemma and morpohoFeats derived from the Lassy dataset (in Dutch). This dataset was extended with insurance related word components and terms that were not included in the Lassy dataset.

* also included are the word components of a term. The Dutch language, like the German language, often glues components together to construct new words instead of using separate words like the English language.
