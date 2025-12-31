# Repo guidance for AI coding agents

Purpose: help an AI agent become productive quickly by summarizing the project's structure,
workflows, conventions and concrete examples found in the repository.

1) Big picture
- This repository is a short bioinformatics lab project centered on a single Jupyter notebook: `estructuras-tridimensionales.ipynb`.
- Primary artifacts: the notebook, a README, PDB/CIF files (e.g. `1a3n.cif`, `pdb1lyz.ent`, `pdb1wso.ent`, `pdb1cq0.ent`) and a BLAST XML output `blast_orexin.xml`.
- The notebook implements protein-structure workflows (download PDBs, parse structures, compute geometric properties, run BLAST, build simple phylogenies).

2) Key libraries & concrete examples (copyable)
- The notebook uses Biopython and NumPy. Example import lines:

    from Bio.PDB import PDBList, PDBParser, MMCIFParser
    from Bio.PDB.vectors import calc_dihedral, calc_angle, Vector
    import numpy as np

- Example PDB download + parse pattern used throughout the notebook:

    pdb_id = "1A3N"
    pdbl = PDBList()
    file_path = pdbl.retrieve_pdb_file(pdb_id, pdir='.', file_format='mmCif')
    parser = MMCIFParser()
    structure = parser.get_structure(pdb_id, file_path)

- BLAST workflow: uses `Bio.Blast.NCBIWWW.qblast` and saves/reads `blast_orexin.xml`. If the XML exists, the notebook reads it locally via `NCBIXML.read()`.

3) Developer workflows (how to run and debug)
- Open and run `estructuras-tridimensionales.ipynb` in Jupyter: `jupyter lab` or `jupyter notebook` and execute cells sequentially.
- If installing dependencies locally, the notebook suggests: `pip install biopython numpy` (cells contain commented `!pip install ...`).
- Network‑dependent steps (BLAST via NCBIWWW) may fail without internet or with NCBI rate limits. Use the existing `blast_orexin.xml` for offline runs.

4) Project conventions & patterns to preserve
- Data files live at repository root and are referenced by name (e.g. `blast_orexin.xml`, `1a3n.cif`). New code should reuse these filenames or update the notebook accordingly.
- Atom/residue selection convention in the notebook: chains accessed as `structure[0]['A']`, residues filtered with `res.id[0] == ' '` (to skip hetero residues).
- When adding Python modules, place them in a new `src/` directory and add a `requirements.txt` listing `biopython` and `numpy` so notebook and scripts share the same deps.

5) Integration points & edge cases
- PDB retrieval: `PDBList().retrieve_pdb_file(..., file_format='pdb'|'mmCif')` writes to current directory; watch for name collisions.
- BLAST: code uses `NCBIWWW.qblast` (remote). Fallback: the notebook expects `blast_orexin.xml` to exist locally and parses it with `NCBIXML`.
- Phylogeny: code builds a simple NJ tree using trimmed sequences and `Bio.Phylo` — trimming to a common minimum length is used before building distance matrices.

6) When editing the notebook or code
- Prefer small, incremental changes. If converting notebook cells to standalone scripts, keep one-to-one correspondence to the notebook sections (1A3N, 1LYZ, Orexinas) and add a README snippet describing how to run them.
- Add `requirements.txt` and update `README.md` if you introduce new dependencies or scripts.

7) What is NOT present / quick notes for contributors
- There is no automated test suite or CI in the repository. Changes to core parsing/geometry code should be validated by running the notebook manually.
- No packaging (no setup.py/pyproject.toml). Treat this repo as a data+notebook lab exercise rather than a library until asked to convert it.

If anything here is unclear or you'd like the instructions to include example tests, CI steps, or a suggested `requirements.txt`, tell me which section to expand.
