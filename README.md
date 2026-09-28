# Can LLMs Predict 3PL Item Parameters in Brazilian Portuguese? An Exploratory Study with ENEM Items

Code, prompts, and detailed results supporting the poster *"Can Large Language Models Predict 3PL Item Parameters in Brazilian Portuguese? An Exploratory Study with ENEM Items."*

**Author:** Pedro Otavio Mantovani ([pedro.mantovani6@usp.br](mailto:pedro.mantovani6@usp.br))
**Advisor:** André C. Ponce de Leon F. de Carvalho ([andre@icmc.usp.br](mailto:andre@icmc.usp.br))
University of São Paulo, Institute of Mathematics and Computer Science (ICMC-USP)

📄 The full poster (with all figures) is available in [`poster.pdf`](poster.pdf).

---

## Overview

Item calibration under Item Response Theory (IRT) traditionally requires a pilot test application, which is costly and risks item leakage. This project explores whether **commercial Large Language Models (LLMs)** can estimate the three parameters of the **3-parameter logistic (3PL) model** — **discrimination (a)**, **difficulty (b)**, and **guessing (c)** — directly from an item's text, using a **few-shot prompting** approach, without any task-specific training.

We tested **3 commercial LLMs** (GPT, Claude, Gemini) on **35 language-domain items** from the Brazilian National High School Exam (ENEM), each prompted with **9 few-shot examples** and run **3 independent times** per item to assess stability.

**Main finding:** difficulty (b) was the only parameter with a consistent, above-chance signal (Pearson *r* up to 0.70). Discrimination (a) and guessing (c) showed near-zero predictive power, with a marked regression-to-the-mean pattern. Notably, the LLM-estimated *a* and *b* values were highly intercorrelated (0.33–0.94) even though the two parameters are essentially uncorrelated in the official calibration — suggesting the models rely on a shared, difficulty-driven heuristic rather than estimating each parameter independently.

## Method

- **Models:** Claude Sonnet 5 (Medium reasoning effort); Gemini 3.1 Pro; GPT‑5.6 Luna. All in free web interface.
- **Data:** 35 items from the 2024 ENEM (language domain only), 9 few-shot examples (from other years chosen to best represent different parameter levels).
- **Runs:** 3 independent runs non-batched per model, per item.
- **Evaluation:** estimates compared against the official 3PL calibration via Pearson *r*, *r²*, bias, MAE, and RMSE
- **Stability:** run-to-run agreement assessed via Intraclass Correlation Coefficient (ICC) and pairwise MAE/SD
- **Ensembling:** averaging the 3 runs was tested as a way to reduce noise, without requiring ground truth to pick the "best" run — the ensemble matched or outperformed the best single run

The [initial prompt](initial_prompt.txt) for every LLM contained the 9 exemple items and the first item to be estimated. After that each item of the list [items.txt](itens.txt) was sent one by one (just the text, without the numeration).

## Results

### Correlation & error metrics per parameter

<table>
<thead>
<tr>
<th rowspan="2">Metric</th>
<th colspan="3">Discrimination (a)</th>
<th colspan="3">Difficulty (b)</th>
<th colspan="3">Guessing (c)</th>
</tr>
<tr>
<th>GPT</th><th>Claude</th><th>Gemini</th>
<th>GPT</th><th>Claude</th><th>Gemini</th>
<th>GPT</th><th>Claude</th><th>Gemini</th>
</tr>
</thead>
<tbody>
<tr><td><em>r</em></td><td>-0.181</td><td>-0.047</td><td>-0.041</td><td>0.372</td><td>0.505</td><td>0.695</td><td>-0.015</td><td>0.093</td><td>0.171</td></tr>
<tr><td><em>p</em></td><td>0.298</td><td>0.789</td><td>0.815</td><td>0.028</td><td>0.002</td><td>&lt;0.001</td><td>0.932</td><td>0.595</td><td>0.326</td></tr>
<tr><td><em>r²</em></td><td>0.033</td><td>0.002</td><td>0.002</td><td>0.139</td><td>0.255</td><td>0.483</td><td>0.000</td><td>0.009</td><td>0.029</td></tr>
<tr><td>Bias</td><td>-1.032</td><td>-0.569</td><td>-0.609</td><td>-0.110</td><td>0.431</td><td>-1.079</td><td>0.032</td><td>-0.001</td><td>-0.004</td></tr>
<tr><td>MAE</td><td>1.194</td><td>1.060</td><td>0.925</td><td>0.521</td><td>0.662</td><td>1.111</td><td>0.062</td><td>0.061</td><td>0.058</td></tr>
<tr><td>RMSE</td><td>1.548</td><td>1.339</td><td>1.258</td><td>0.635</td><td>0.851</td><td>1.247</td><td>0.081</td><td>0.074</td><td>0.073</td></tr>
</tbody>
</table>

*r*: Pearson correlation with the official parameter value. *r²*: coefficient of determination. Bias: mean signed error (estimate − official value). MAE / RMSE: mean absolute / root mean squared error.

### Run-to-run stability (difficulty parameter, *b*)

Computed across the three independent runs — the only parameter with a strong enough signal for this analysis to be meaningful.

**Intraclass Correlation Coefficient (ICC), all three runs combined:**

| Metric | GPT | Claude | Gemini |
|---|---|---|---|
| ICC | 0.60 | 0.93 | 0.69 |

**Pairwise MAE and SD between runs:**

<table>
<thead>
<tr>
<th rowspan="2">Metric</th>
<th colspan="3">Run 1 &amp; 2</th>
<th colspan="3">Run 1 &amp; 3</th>
<th colspan="3">Run 2 &amp; 3</th>
</tr>
<tr>
<th>GPT</th><th>Claude</th><th>Gemini</th>
<th>GPT</th><th>Claude</th><th>Gemini</th>
<th>GPT</th><th>Claude</th><th>Gemini</th>
</tr>
</thead>
<tbody>
<tr><td>MAE</td><td>0.35</td><td>0.26</td><td>0.67</td><td>0.32</td><td>0.47</td><td>0.77</td><td>0.29</td><td>0.36</td><td>0.45</td></tr>
<tr><td>SD</td><td>0.41</td><td>0.30</td><td>0.85</td><td>0.44</td><td>0.34</td><td>0.94</td><td>0.37</td><td>0.29</td><td>0.47</td></tr>
</tbody>
</table>

Despite this run-to-run variability, averaging estimates across the three runs (ensembling) mitigated noise and matched or outperformed the best single run for every model.

## References

- Belov, D. I., Topczewski, A., & McVay, A. (2022). Predicting IRT 3PL parameters via a neural network model. *Proceedings of the International Meeting of the Psychometric Society (IMPS)*, Bologna, Italy.
- Benedetto, L. (2023). A quantitative study of NLP approaches to question difficulty estimation.
- Ferrara, S., Steedle, J. T., & Frantz, R. S. (2022). Response demands of reading comprehension test items: A review of item difficulty modeling studies. *Applied Measurement in Education*, 35(3), 237–253.
- Godoy, H. (2025). Alvorada-bench: Can language models solve Brazilian university entrance exams?
- INEP (2021a). Entenda a sua nota no ENEM: guia do participante. Instituto Nacional de Estudos e Pesquisas Educacionais Anísio Teixeira, Brasília, DF.
- INEP (2021b). Exame Nacional do Ensino Médio (ENEM): procedimentos de análise. Instituto Nacional de Estudos e Pesquisas Educacionais Anísio Teixeira, Brasília, DF.
- Lang, J. W. B., & Tay, L. (2021). The science and practice of item response theory in organizations. *Annual Review of Organizational Psychology and Organizational Behavior*, 8, 311–338.
- Maeda, H. (2025). Field-testing multiple-choice questions with AI examinees: English grammar items. *Educational and Psychological Measurement*, 85(2), 221–244.
- Ulitzsch, E., Belov, D., Lüdtke, O., & Robitzsch, A. (2026). Using item parameter predictions for reducing calibration sample requirements—A case study based on a high-stakes admission test. *Journal of Educational Measurement*, 63(1), e12426.

## Acknowledgements

This research was developed within the framework of the National Institute of Artificial Intelligence for Social Good (IAPROBEM) Project (INCT), funded by CNPq (Grant No. 408589/2024-8), and with support from FAPESP (Grant No. 2026/04096-2).

The travel for the congress was supported by Institute of Mathematics and Computer Science (ICMC-USP).

## Contact

Pedro Otavio Mantovani — [pedro.mantovani6@usp.br](mailto:pedro.mantovani6@usp.br)
Advisor: André C. Ponce de Leon F. de Carvalho — [andre@icmc.usp.br](mailto:andre@icmc.usp.br)
