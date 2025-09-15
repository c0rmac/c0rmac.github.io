{% include base_path %}

Education
======
* Technical University of Munich 2023-2026
  * Masters in Mathematics
* Trinity College Dublin, 2019-2023
  * B.A in Mathematics and Statistics
* Technical University Munich, 2021-2022
  * 1-year Masters Programme in Data Analytics & Machine Learning
* Leaving Certificate in Grammar School Dundalk, 2017-2019
    * 567 Points (Top 3% in Ireland)


Open‑Source Libraries
======
### <a href='https://github.com/c0rmac/qtexture'>qtexture</a>
High‑performance toolkit for computing quantum‑state texture measures and basis‑minimized resource monotones. Compact C/C++ core with Python bindings, designed for numerical stability and integration into researcher workflows.  
- Basis‑dependent texture measures and basis‑minimized monotones for density matrices.  
- Density‑matrix‑native optimizer ProgQAOA with adaptive heuristics (Adaptive Layering, Freeze‑then‑Refine).  
- Optional GPU acceleration (Metal) and C/C++ kernels for hotspots.  
- Lightweight interoperability utilities for Qiskit and QuTiP; minimal NumPy/SciPy dependency footprint.  

### <a href='https://github.com/c0rmac/holographic-tensor-networks'>holographic‑tensor‑networks</a>
Numerical laboratory for holographic tensor networks built on discrete hyperbolic geometry (hyperbolic buildings). Converts combinatorial geometric constructions into contractible tensor networks and provides flexible numerics for entanglement and holographic diagnostics.  
- Programmatic geometric kernel (HyperbolicBuilding) to generate `{p,q}` tilings, construct buildings from side‑pairings, enumerate faces and dual graphs, and locate discrete geodesics.  
- Automated tensor‑network builder that places tensors on faces and assembles contractions from the building’s topology to produce boundary states.  
- Multiple contraction/entropy strategies: Kernel Polynomial Method (KPM) for memory‑efficient stochastic trace estimation, exact diagonalization for small systems, and Block Belief Propagation (BBP) as an approximate tree‑aware solver.  
- CPU (NumPy/Quimb/Yastn) and optional GPU (PyTorch) backends; utilities for RT‑style diagnostics and minimal‑surface / cut‑length reporting.  

---

Algorithmic Contributions
======
### <a href='https://github.com/c0rmac/interlink-alg'>interlink‑alg</a>
Streaming, memory‑efficient algorithm to extract equivalence classes of atomic symbols from a dictionary of sequences by grouping characters that co‑occur (directly or transitively) across words.  
- Builds a character co‑occurrence graph then identifies connected components as identifier sets.  
- Streaming and low‑latency variants for large corpora with implementations in C++, Python, and Rust.  
- Complexity: O(M · L²) to build the co‑occurrence map (M = number of words, L = average word length) plus graph traversal costs dependent on alphabet size.  
https://github.com/c0rmac/interlink-alg

### <a href='https://github.com/c0rmac/react-quiz-and-progress'>react‑quiz‑and‑progress</a>
Algorithm for computing questionnaire completion progress in a way that reflects both independent and dependent questions, giving a more accurate and intuitive measure of progress in branching surveys.  
- Models the questionnaire as a directed dependency graph, where dependent questions only contribute to progress if their prerequisites are met.  
- Assigns weighted increments so that progress advances proportionally to the number of relevant questions, not the total possible questions.  
- Handles branching logic seamlessly — progress changes only when a new path is unlocked, avoiding misleading percentage spikes or stalls.  
- Designed for reusability in any survey or decision‑tree workflow, independent of presentation layer.  
https://github.com/c0rmac/react-quiz-and-progress

---

Professional Experience
======
### BMW — Software Engineer (June 2023-Present)

- Architected a software solution that centralized complex engineering and test data into a large and sophisticated interactive dashboard, enabling domain experts to explore and analyze datasets in real time.  
- Designed and implemented internal numeric libraries to compute specialized quantities for battery testing, ensuring numerical stability and reproducibility.  
- Developed advanced libraries and frameworks to simplify front‑end integration for colleagues, reducing implementation time for complex visual components.  
- Created high‑performance visualization algorithms to render large volumes of intricate electrical engineering data in a form usable by engineers for diagnostics and decision‑making.  
- Collaborated cross‑functionally with electrical engineers, data scientists, and front‑end developers to ensure seamless integration of computational back‑end and visualization layers.

### BCMS Upload Services --- Mid-experienced Software Engineer (June 2020 - June 2023)
  * Application that assists civil engineers with online government bureaucracy by automating the upload process of construction documents and thus reducing the time of submission from one hour to only a few minutes.
  * Tasked with creating the user interface, back-end functionality and working with engineers to develop a finished product.
  * Kotlin-ReactJS/Spring web application.
  * See [here](https://bcms-upload.ie "here") for more.

### The Rob of the Green Podcast --- Software Developer & Online Business Consultant (April 2021 - August 2021)
  * Podcast website for a team of award-winning life coaches.
  * Tasked with completely redesigning the podcast website from scratch, implementing an efficient & long-lasting website
backend and migrating old podcasts & blog posts from the old websites to the new one.
  * Consulted end-user on website structuring and advertisement strategies.
  * WordPress web application with use of bootstrap css.
  * See [here](https://robofthegreen.ie) for more.

### Reactoo --- iOS Software Analyst (June 2017 - August 2017)
  * Reactoo is an emerging social media platform based on video streaming amongst multiple users.
  * Hired as software analyst working with in a small software development team.
  * Tasked with implementing core features such as video & face tracking filters, bug fixing and automating app tests.
  * Obtained valuable teamwork experience with a team of highly experienced developers.
  * Swift iOS Application using AWS APIs to interact with backend. Involved the use of video encoding APIs.  
  * See [link](https://reactoo.com) for more.

### Turner & Townsend --- Software Analyst (March 2017 - April 2017)
  * Turner & Townsend is a multinational professional service specializing in programme, project and cost management and
consulting across the global property market and natural resources sectors.
  * Tasked with developing an app to replace physical hand-out cards of health & safety tips used for promoting a safety conscious work environment. Both apps developed using native sdk.
  * Implemented a data table on the company’s website as well as modifying their graphical API using Google Analytics.

---

Education Work Experience
======
### Hewitt College Cork --- Education Consultant (January 2021 - July 2021)
  * In charge of designing the template for Physics revision videos in Irish for past Leaving Certificate papers.
  * Lead a team of private tutors to produce and voice the videos.
  * Created highly comprehensive solutions and explanations to past paper questions.

---

Personal Projects
======
### Law of Large Numbers & Data Mining applied to Crypto Alt‑coins
End‑to‑end research program combining unsupervised regime discovery, stability‑focused model selection, and a deterministic live‑trading pipeline.  
- Regime signal: K‑Means clustering on engineered features (candlesticks, volume, sentiment trajectories, rolling‑mode Fear & Greed trails) to produce pseudo‑stationary partitions for regime‑conditioned models.  
- Selection heuristic: large‑scale "Grand Tournament" of shuffled training runs with stratified cross‑validation; champion models selected by minimum validation‑variance to prioritise stability over single‑run accuracy.  
- Execution: deterministic two‑phase trade lifecycle — pre‑trade momentum confirmation followed by active trade management with dynamic trailing stops, profit targets, divestment pools and reinvestment rules.  
- Risk and deployment: compartmentalized capital pools, dry‑run ↔ live conversion logic, ensemble deployment options to reduce per‑cycle variance via the Law of Large Numbers.  
- Backtesting: parallelized, tick‑level replay and statistical aggregation of outcome distributions for robust empirical evaluation.  

References:  
- [ClusterBuster — Unsupervised Regime Discovery and Cluster Scoring](https://c0rmac.github.io/posts/2025/8/15/chapter-1-cluster-buster/)
- [Improving Odds — Stability‑Focused Selection Heuristic](https://c0rmac.github.io/posts/2025/8/15/chapter-2-improving-odds/)
- [Deterministic Signaling Live Trader — Execution Architecture and Backtesting](https://c0rmac.github.io/posts/2025/8/15/chapter-3-deterministic-signaling-live-trader/)

### Crash detection of alt coins through Topological Data Analysis
Complementary diagnostic pipeline to flag regime shifts and extreme events for crypto assets.  
- Core steps: Takens time‑delay embedding → sliding‑window point clouds → Vietoris‑Rips persistence (H0, H1) → homological‑derivative features computed from landscape and Betti‑curve distances.  
- Role: topology‑aware indicators for risk management and to augment selection signals (alerts for anomalous topology prior to or during crashes); implemented as scikit‑learn‑style transformers for easy integration with the trading stack.

### House of Costs
  * Application that estimates the cost of a renovation with a given design by filling in a questionnaire of your house/room.
  * Works for both construction engineers and customers to provide cost estimates with varying detail depending on expertise.
  * Developed UI and backend structure, and a compiler that compiled renovation information with corresponding designs,
which were encoded in an Excel spreadsheet. Developed admin API for my engineer colleagues to compile spreadsheets.
  * Questionnaire and background calculations created by a professional construction engineer. Statistics & Mathematical
models developed with my B.A. training.
  * Kotlin Multiplatform-ReactJS/Spring web application.

### Gaeilge Chun Cinn
  * Initiative I am undertaking with 5 colleagues of mine to translate everyday apps, websites, and games into Irish Gaelic.
  * Accomplishments so far:
    * Launched an official Irish translation for the block buster game Amongst Us. See our [Verge article](https://www.theverge.com/2021/7/16/22579968/among-us-official-irish-translation "Verge article").
  * Developed the Wordpress website myself.
  * International team, with our volunteer translators based Norway, New Zealand, Ireland, Germany, Russia & USA.
  * Gained a lot of experience with team work and team management.
  * See [link](http://gaeilgechuncinn.com/en/ "here") for more.

### Focal.ie
  * Launched one of my very first apps when I was 14 – a Gaeilic dictionary for high schools.
  * Drew attention of the [headlines](https://www.independent.ie/regionals/argus/entertainment/appy-launch-for-teen-31017372.html "headlines")

---

Skills, Activities & Interests
======
* Very strong Kotlin, Java, Swift, SQL background – Spring Boot, ReactJS for Kotlin.
* Strong C++, Rust.
* Applied Statistics, Data Analytics & Machine Learning qualifications.
* Bilingual in English and Irish.
* Asked in 2014 by Minster for Training & Skills Ciarán Cannon to be a founding member of the Digital Youth Council.

* Interests include traveling, reading, hiking and learning foreign languages.