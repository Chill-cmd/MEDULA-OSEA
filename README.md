# Hematopoietic Physiology Lab

An interactive, single-file web simulator for exploring the physiology of erythrocytes, leukocytes, monocytes, and antigen-presenting cells (CPA) — grounded in bone marrow MRI physiology and core hematology/immunology teaching.

This is a simulator, not a slideshow: every screen is built around something the student does (drag an age slider, play a maturation sequence, choose an MHC pathway, run a timed exam) rather than something they only read.

## How to run it

No build step, no dependencies, no server required.

1. Download/clone this repository.
2. Double-click `index.html`, or open it directly in any modern browser (Chrome, Firefox, Edge, Safari).
3. Optional (only needed if you want it served over `http://` instead of `file://`, e.g. to test on a phone on the same network):
   ```bash
   python3 -m http.server 8000
   # then open http://localhost:8000
   ```

Everything — layout, data, and logic — lives in `index.html`. There is nothing to install and no internet connection is required (the only external resource is an optional Google Fonts stylesheet for typography; the app falls back to system fonts if it can't be reached).

## Architecture

- **Single-page app, vanilla JavaScript.** No framework, no build tooling. A tiny hash-based router (`#mri`, `#eryth`, `#apc`, ...) swaps the content of `<main id="app">`.
- **Data-driven rendering.** Each module's content (marrow composition, MRI signal behavior, erythropoiesis stages, leukocyte types, extravasation steps, MHC scenarios, the question bank, etc.) lives in plain JS data objects/arrays near the top of the script. The render functions are generic (a stepper engine, an MCQ engine, a tabs engine) and consume that data — so the visuals, quiz bank, and clinical cases can be extended by editing data, not by rewriting UI code.
- **All graphics are inline SVG/CSS**, generated at runtime (a schematic skeleton, a long-bone diagram, an erythroid cell that morphs across maturation stages, a live oxyhemoglobin dissociation curve computed from a Hill-equation approximation, etc.) — nothing is a static image.
- **No backend, no tracking, no accounts.** All state (quiz progress, missed-concept tracking) lives in memory for the session.

## Modules implemented

01. **Bone Marrow MRI** — red vs. yellow marrow composition, a T1/T2/STIR signal simulator with "Why?" / "Compare" / isolate-tissue controls, an age-conversion slider (fetal → adult) driving a schematic skeleton and long-bone diagram, and a "zoom into active marrow" sequence bridging radiology to cell biology.
02. **Hematopoiesis** — a clickable stem-cell lineage tree (myeloid vs. lymphoid), with progressive disclosure (origin/function/location/characteristics/fate) per cell.
03. **Erythropoiesis** — a 10-stage Play/Pause/Next/Back/Reset maturation sequence (stem cell → mature erythrocyte) with live size/chromatin/hemoglobin readouts, plus an EPO feedback simulator (tissue O2, renal function, iron, B12, folate).
04. **Erythrocyte Physiology** — structure and deformability, a live oxyhemoglobin dissociation curve (PO2/pH/PCO2 sliders, Bohr effect), the CO2/chloride-shift sequence, and a 120-day lifespan timeline ending in splenic/hepatic clearance and iron recycling.
05. **Leukocytes** — five selectable cell types (neutrophil, eosinophil, basophil, lymphocyte, monocyte) and a step-by-step extravasation simulator (margination → rolling → activation → adhesion → diapedesis → chemotaxis).
06. **Monocytes** — blood → tissue → macrophage differentiation, a phagocytosis animation, and toggled info on cytokine production, debris clearance, repair, and antigen presentation.
07. **Antigen-Presenting Cells** — dendritic cell / macrophage / B lymphocyte overview, an antigen-presentation stepper, an MHC I vs. MHC II decision simulator (with an optional cross-presentation scenario), and an innate → adaptive immunity sequence.
08. **Clinical Connection** — four short reasoning cases (hypoxia and EPO, renal failure and erythropoiesis, ordering the steps of extravasation, MHC II and CD4+ recognition), with immediate feedback and explanations.
09. **Test Yourself** — a practice mode (random questions, weighted toward concepts you've missed, with a live "concepts to revisit" list) and a timed Exam Mode (18 questions, immediate feedback, final score, missed-question review, and topics to review).

## Sources

**Bone Marrow MRI module (composition percentages, T1/T2/STIR signal behavior, age-related marrow conversion pattern) is based directly on:**

> Londoño MA, Vallejo JM, Manzano AC. *Desarrollo y maduración normal de la médula ósea, valoración por resonancia magnética.* Rev Colomb Radiol. 2015;26(2):4206-12.

Figures were not copied; the concepts (red vs. yellow marrow composition, T1/T2/STIR signal characteristics, and the fetal → newborn → 1y → 10y → adolescent → adult conversion pattern) were reproduced as original schematic diagrams and interactive controls. The skeleton/long-bone age-conversion diagram beyond the exact stages described in the article (e.g. some skull/rib timing within adolescence) is a reasonable didactic simplification, explicitly labeled as such in the app.

**All other physiology and immunology content** (hematopoiesis, erythropoiesis stage morphology, erythrocyte structure and O2/CO2 transport, leukocyte function and extravasation, monocyte/macrophage biology, and antigen presentation/MHC I–II) reflects standard teaching found in the course bibliography: Guyton & Hall (*Tratado de Fisiología Médica*), Ruiz Argüelles (*Fundamentos de Hematología*), Sánchez Guijo, Abbas (*Cellular and Molecular Immunology*), and Langman (*Embriología Médica*), among other standard hematology/immunology references. No specific figures, numbers, or citations from these texts were fabricated; where a numeric value is shown (P50, RBC lifespan, differential percentages, etc.) it reflects widely-taught reference values, and interactive physiological models (the oxyhemoglobin curve, the EPO feedback loop) are explicitly labeled as simplified teaching schematics rather than patient-specific measurements.

## Important scientific-accuracy note

The MRI module is deliberately careful to **never imply that MRI can show individual hematopoietic cells.** A persistent disclaimer states this on every screen of Module 01: MRI demonstrates bone marrow *composition and distribution* (red vs. yellow marrow, signal behavior across sequences), not BFU-E, CFU-E, erythroblasts, reticulocytes, or individual leukocytes — those are shown only in the separate, explicitly cellular/microscopic modules (02–07), reached through a labeled "zoom" transition that marks the shift from radiology to cell biology.

## Extending it

To add a quiz question, add an object to the `QUESTION_BANK` array. To add a clinical case, add one to `CLINICAL_CASES`. To add a leukocyte type, cell stage, or MHC scenario, add an entry to the corresponding data array near the top of the `<script>` block — the render/quiz engines pick it up automatically.
