## Tasks Done:

1. Blasted current S.Div assembly ([given at this link](https://ucdavis.app.box.com/folder/223780038618)) against Arabidopsis FLOE 1, 2, and 3's  "Full length CDS" and "protein" from [TAIR](https://www.arabidopsis.org/).

2. Put blast results onto [this spreadsheet](https://docs.google.com/spreadsheets/d/1oBC-fnAcyAyqWPGcrLjeC9oqIf-78krFuIXgaVq4L6s/edit#gid=1075213907) and extracted the best matching sequences for each FLOE with a buffer of 500 nts on each side. Three files were made, one for each of the FLOE and it's best matching sequences:
- for FLOE 1: ptg000004l and ptg000005l
- for FLOE 2: ptg000009l and ptg000010l
- for FLOE 3: ptg000001l and ptg000009l and ptg000010l and ptg000013l

3. Had to transform ptg000009l into reverse complement.

4. Used UGENE to aligned each file with mafft. (Also looked with other aligners, but did nothing with those.)

5. Cleaned up the files by trimming out everything except for the exons.

6. Used UGENE to combine and align all of the sequences (arabidopsis and my trimmed exons).

7. Used that file to make a tree. There were duplicates for ptg000009l and ptg000010l, we only kept the ones that matched to FLOE 2.

8. Blasted my trimmed FLOE files against original S.Div assembly to get coordinates. Results on [this spreadsheet](https://docs.google.com/spreadsheets/d/1MEioFX27GedKDvz1TXvmDE-5LHpwSeCBO2dFApO3jkA/edit#gid=2026339224).

9. Blasted my trimmed FLOE files against predicted S.div transcript. Results on [this spreadsheet](https://docs.google.com/spreadsheets/d/1wcAUnhzGyigyVHJq5FEKoF7_xBeTSmZWoa-nMzfXWpw/edit#gid=2026339224).

10. There was missing information on ptg000013l in the gff file. Added gene, exon, and CDS to the file.

11. Now working on Differential Gene Expression from RNAseq. See scripts folder.