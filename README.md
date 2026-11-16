# CRLP: Capacitated Resource Leveling Problem

This repository provides the datasets, task schedules, resource profiles, and computational results accompanying the study **An effective iterated local search algorithm for the capacitated resource leveling problem on cloud platforms**.

The capacitated resource leveling problem (CRLP) determines task start times under resource capacity, start-time window, and precedence constraints, given time-varying task demands and an existing fixed-load profile. Its objective combines segment peak costs, target-deviation costs with time-dependent unit costs, and task-placement costs through a normalized weighted sum.

## Datasets and experiments

| Dataset | Projects | Tasks per project |
| --- | ---: | --- |
| Synthetic | 25 | 300, 600, 1,200, 3,600, and 5,000; five projects per size |
| Industrial | 10 | 220–6,860 |

Each project uses a one-day planning horizon with 1,440 one-minute time slots. The main comparison evaluates all 35 projects under three objective preference scenarios, giving 105 project–scenario instances. The results also include repeated runs, component ablations, parameter sensitivity analyses, and industrial case studies.

## Repository contents

| Location | Contents |
| --- | --- |
| [raw_data/](raw_data/) | Task demands, time windows, precedence relations, fixed loads, resource capacities, and project statistics. |
| [results/output_schedule/](results/output_schedule/) | Main-comparison schedules and load profiles, best-known solutions, and shared initial schedules. |
| [results/compare_result/](results/compare_result/) | Main and repeated comparisons, including results grouped by project size, solver optimality status, and resource scarcity. |
| [results/ablation_result/](results/ablation_result/) | Component ablations, convergence summaries, operator acceptance rates, and evaluation-time comparisons. |
| [results/parameter_sensitivity_result/](results/parameter_sensitivity_result/) | Individual seeded runs, median objectives, and boxplot statistics. |
| [results/case_study/](results/case_study/) | Weight-response results and the resource profiles used in the industrial case figures. |
| [results/figures/](results/figures/) | Figures from the main text and appendices, named using their figure numbers and captions. |

## Reading the data and results

Start with the [dataset documentation](raw_data/ReadMe.md) for input schemas, resource units, and time conventions. The [results documentation](results/ReadMe.md) explains output fields, objective scenarios, algorithm identifiers, initial-schedule baselines, and the experimental summary tables.

Project identifiers are consistent across the datasets and results. For example, `J220` identifies an industrial project with 220 tasks, while `J300_1` identifies one of the synthetic projects with 300 tasks. Read task and project identifiers as text.

Time indices are zero based, and task start and finish indices are inclusive. CSV files use UTF-8 encoding; result CSVs include a byte-order mark. Best-known solutions are the best feasible results among the included main-comparison runs and are not necessarily proven optima.
