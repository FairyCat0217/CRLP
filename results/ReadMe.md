# Computational results for the CRLP

This package contains the schedules, resource profiles, experimental result tables, and figures from the main text and appendices for the capacitated resource leveling problem. Project identifiers match those in the accompanying `raw_data` package. There are 25 synthetic projects and 10 industrial projects. An industrial identifier such as `J220` denotes a project with 220 tasks.

## Package contents

| Directory | Contents |
| --- | --- |
| `output_schedule/industrial_main_compare/` | Main-comparison outputs for the 10 industrial projects, grouped by algorithm and scenario. |
| `output_schedule/synthetic_main_compare/` | Main-comparison outputs for the 25 synthetic projects, grouped by algorithm and scenario. |
| `output_schedule/bks/` | Best-known schedules and profiles for the 105 project-scenario instances, plus their result summary. |
| `output_schedule/init/initial_schedule/` | One initial schedule, resource-profile CSV, and plot for each of the 35 projects. |
| `output_schedule/init/merged_summary.csv` | Evaluation of those initial schedules under all three objective scenarios: 105 records. |
| `compare_result/` | Main, repeated, size-based, Gurobi-group, and resource-scarcity comparisons. |
| `ablation_result/` | Search-component ablations, convergence curves, operator acceptance, and evaluation-timing results. |
| `parameter_sensitivity_result/` | Individual seed runs, four-seed medians, and boxplot statistics for the two sensitivity analyses. |
| `case_study/` | Weight-response results for Figure 8 and the minute-level profiles used in Figures 9 and D2. |
| `figures/` | Main-text and appendix figures, numbered and named using their captions. |

CSV files use UTF-8 with a byte-order mark. Identifiers should be read as text. Empty cells and `None` indicate unavailable or inapplicable values.

## Scenarios, algorithms, and objective values

`scene_id` takes values 1, 2, and 3; `scene` may use the equivalent labels `S1`, `S2`, and `S3`. The preference vectors are:

| Scenario | Peak, deviation, and placement preference coefficients |
| --- | --- |
| S1 | (55, 5, 40) |
| S2 | (100, 0, 0) |
| S3 | (10, 90, 0) |

`peak_obj`, `deviation_obj`, and `placement_obj` are the three unnormalized cost components. `obj` is the normalized weighted objective to be minimized. Where present, `preference` gives the three preference coefficients, `scale` gives the reference scales, and `weight` gives the normalized coefficients in peak-deviation-placement order. Thus `obj` is the weighted sum of the three cost components.

In `merged_summary*.csv`, `before_obj` is the objective value of the shared initial schedule supplied for that project and scenario. `initial_obj` is the objective value of the schedule used to initialize the algorithm. For all algorithms other than ILS-cold, these two values are equal. ILS-cold first searches for a feasible schedule and uses it to initialize the optimization search, so its `initial_obj` can differ from `before_obj`. `init_violation` records the resource violation of the algorithm's initialization schedule. All reported `init_violation` values are zero, and all initialization schedules are feasible.

The main comparison includes ILS, ILS8, ILS-cold, Gurobi, Gurobi-LNS, Gurobi-LNS3600, GA-TLIM, Hybrid-SER, and IAHS. Underscores in folder names correspond to hyphens in the paper's algorithm names. `Init` in aggregate tables is the initial-schedule reference. ILS8 returns the best result from eight independent workers.

`bks_obj` is the best-known objective from the main comparison; it is not necessarily a proven optimum. `gap_to_bks_pct` is `100 * (obj - bks_obj) / bks_obj`. Fields ending in `_pct` are percentages. The solver's `gap` is a relative MIP-gap fraction, and `obj_bound` is its objective bound; `gurobi_mip_gap_pct` expresses that gap as a percentage. Raw `improvement_rate` values are fractions, calculated as `round((before_obj - obj) / before_obj, 4)` using the shared initial schedule. The `time` field records runtime in seconds.

## Schedule files: `*_final_schedule.csv`

Each row describes one task.

| Field | Meaning |
| --- | --- |
| `task_id` | Task identifier, shared with the task and precedence data. |
| `project_id` | Project identifier, matching the filename. |
| `start_time` | Task start in the reported schedule. |
| `end_time` | Task finish in the reported schedule, inclusive. |
| `run_time` | Duration in time slots: `end_time - start_time + 1`. |
| `cpu_used_detail` | Task-relative demand vector in NumPy array notation: space-separated values in brackets, possibly spanning multiple lines. The first entry corresponds to `start_time`. |
| `before_start_time` | Task start in the shared initial schedule. |
| `before_end_time` | Task finish in the shared initial schedule, inclusive. |
| `begin_time` | Task-specific earliest allowed start, matching `earliest_start_time` in the task data. |
| `l_begin_time` | Task-specific latest allowed start: `deadline_time - run_time + 1`. |

Time indices are zero based: `0,...,1439` for the one-day horizon, with one minute per slot. Start and end indices are inclusive. Task demand, fixed load, and resource capacity within each slot use CU·h. One CU·h represents using one compute unit for one hour; one compute unit contains one CPU core.

## Resource profiles: `*_final_consuming.csv`

Each file has 1440 rows. Row 1 is time slot 0, row 2 is slot 1, and so on.

| Field | Meaning |
| --- | --- |
| `unsche` | Fixed resource load. |
| `before` | Total resource load of the shared initial schedule, including fixed load. |
| `after` | Total resource load of the reported schedule, including fixed load. |
| `capacity` | Available resource capacity. |

The corresponding `*_final_figure.png` plots these four profiles. The 35 initial schedules are shared across scenarios; their objective evaluations differ with the preference coefficients in `merged_summary.csv`. In the BKS summary, `method` gives the source algorithm name, `source_schedule` points to its main-comparison schedule relative to this results package, and `schedule_file` is relative to `output_schedule/bks/`. Initial-summary file references are relative to `output_schedule/init/`.

## Checkpoint files: `merged_checkpoints*.csv`

| Field | Meaning |
| --- | --- |
| `project_id` | Project identifier. |
| `scene_id` | Objective scenario. |
| `method` | Algorithm identifier. |
| `seed` | Random seed, where recorded. |
| `checkpoint_time` | Requested elapsed-time checkpoint, in seconds. |
| `available_time` | Elapsed time when the recorded incumbent became available, in seconds. |
| `best_obj` | Best objective value recorded at that checkpoint. |

## Main and repeated comparisons

The main comparison covers 35 projects under three scenarios: 105 instances. The repeated comparison uses seeds 42, 101, 202, and 303 for ILS, ILS-cold, Gurobi-LNS, and GA-TLIM, giving 1680 individual runs.

| Files in `compare_result/` | Interpretation |
| --- | --- |
| `algorithm_comparison_detail.csv` | One record per main-comparison algorithm and project-scenario instance. |
| `algorithm_comparison_summary.csv` | Dataset/scenario means, runtimes, improvement rates, and BKS counts. |
| `algorithm_instance_gaps_and_gurobi_groups.csv` | Per-instance gaps and grouping by whether the MIP gap is zero. |
| `algorithm_gaps_by_gurobi_group.csv` | Mean algorithm gaps within those Gurobi groups. |
| `algorithm_comparison_by_size.csv` | Synthetic-project size groups and mean gaps to BKS. |
| `repeat_comparison_runs.csv` | All 1680 individual seeded runs. |
| `repeat_comparison_scene_means.csv` | Mean objective over four runs for each algorithm and project-scenario instance, with the corresponding BKS gap. |
| `repeat_comparison_project_means.csv` | Project-level comparison, averaging the three scenario gaps. |
| `resource_scarcity_ils_vs_best_heuristic_by_scene.csv` | Resource scarcity and ILS improvement over the best compared population heuristic for each project-scenario instance. |
| `resource_scarcity_ils_vs_best_heuristic_by_project.csv` | Project-level scarcity and improvement averaged over scenarios. |

The resource scarcity index is total movable demand divided by capacity remaining after fixed load, both summed across the horizon.

## Ablation studies

| Files in `ablation_result/` | Interpretation |
| --- | --- |
| `ablation_table.csv` | Component-ablation gaps to Full under S1-S3 and their overall average. |
| `ablation_gap_to_full.csv` | Corresponding dataset/scenario gap summaries. |
| `ablation_checkpoint_trends.csv` | Mean normalized best-so-far objective over elapsed time. |
| `operator_acceptance_by_project.csv` | Per-project/scenario/operator call counts and acceptance rates. |
| `operator_acceptance_by_scene.csv` | Operator acceptance averaged across projects for each scenario. |
| `evaluation_ablation_runs.csv` | Candidate-evaluation timing and throughput for each project, scenario, and evaluation method. |
| `evaluation_ablation_summary.csv` | Timing, throughput, and relative evaluation-time summaries. |

`Full` is the complete ILS. A Unicode minus sign in a configuration name indicates a disabled component: `−Pert`, `−TC`, and `−PM` denote removal of perturbation, capacity tightening, and polar moves, respectively. Combined labels disable multiple components. For the component ablation, objectives are averaged over four independent runs before calculating percentage gaps to Full. Positive gaps indicate worse objectives than Full.

An operator call is accepted when it improves the objective. Per-project acceptance rates average over seeds in which that operator was called; scenario-level rates then average across projects. `operator` corresponds to the operator IDs in the paper, `capacity_strategy` identifies its capacity rule, and `polar_shift` indicates whether it uses a polar move.

The evaluation ablation compares Incremental, Cached Full, and Rebuilt Full on six projects and three scenarios using the same seeded search trajectory. Its `mode` field identifies the evaluation implementation; timing fields are in seconds and `evaluation_throughput_k_per_s` is in thousands of candidates per second.

## Parameter sensitivity

The two parameters are `soft_reset_ratio` and `start_time_scan_step`. Their tested settings are (0.1, 0.3, 0.5, 0.7, 0.9) and (1, 3, 5, 7, 9), with defaults 0.5 and 5, respectively.

| Files in `parameter_sensitivity_result/` | Interpretation |
| --- | --- |
| `parameter_sensitivity_run_details.csv` | All 4200 individual runs: 35 projects, three scenarios, two parameters, five settings, and four seeds. |
| `parameter_sensitivity_boxplot_data.csv` | One four-seed median per project-scenario-parameter setting: 1050 records. |
| `parameter_sensitivity_boxplot_statistics.csv` | Quartiles, whiskers, outlier counts, and related scenario/setting summaries: 30 records. |

`median_obj` is the median objective over four seeds. `default_median_obj` is the matching median at the default parameter setting. `relative_change_to_default_pct` equals `100 * (median_obj - default_median_obj) / default_median_obj`. Thus the boxplot statistics describe changes between four-seed medians, rather than individual seed runs. `seed_count` gives the number of runs underlying a median.

## Industrial case study

`case_study/preference_objective_response.csv` contains the 21 weight configurations used in Figure 8 for project `J6860`. `q1`, `q2`, and `q3` are the peak, deviation, and placement preferences. The cost columns use the same definitions as the other result summaries; `f1_normalized`, `f2_normalized`, and `f3_normalized` are the unweighted component costs divided by their reference scales.

`case_study/s_1/`, `s_2/`, and `s_3/` contain `J6860_final_consuming.csv` for Figure 9 and `J645_final_consuming.csv` for Figure D2. Each has the same 1440-row, four-column format described above. These are the actual case-study profiles used in the figures.

## Figures and industrial cases

The figures follow the numbering in the main text and appendices. 

| Figure | Caption | File |
| --- | --- | --- |
| 1 | Example of three objectives. | [Figure 1](<figures/figure 1 - Example of three objectives..png>) |
| 2 | Illustration of capacity strategies and polar moves. | [Figure 2](<figures/figure 2 - Illustration of capacity strategies and polar moves..png>) |
| 3 | Algorithm comparison grouped by MIP optimality status. | [Figure 3](<figures/figure 3 - Algorithm comparison grouped by MIP optimality status..png>) |
| 4 | Algorithm performance trends. (a) Average gap to BKS by project size on synthetic instances. (b) ILS relative improvement vs. resource scarcity index on synthetic and industrial instances. | [Figure 4](<figures/figure 4 - Algorithm performance trends. (a) Average gap to BKS by project size on synthetic instances. (b) ILS relative improvement vs. resource scarcity index on synthetic and industrial instances..png>) |
| 5 | Comparison of ILS and strong baselines over four independent runs per instance under a common 300-second search time limit. (a) Distribution of gaps to BKS. (b) Average gap to BKS by project. | [Figure 5](<figures/figure 5 - Comparison of ILS and strong baselines over four independent runs per instance under a common 300-second search time limit. (a) Distribution of gaps to BKS. (b) Average gap to BKS by project..png>) |
| 6 | Convergence of ILS ablation configurations under three objective scenarios. | [Figure 6](<figures/figure 6 - Convergence of ILS ablation configurations under three objective scenarios..png>) |
| 7 | Parameter sensitivity result of ILS. | [Figure 7](<figures/figure 7 - Parameter sensitivity result of ILS..png>) |
| 8 | Objective responses to preference changes in an industrial scheduling case | [Figure 8](<figures/figure 8 - Objective responses to preference changes in an industrial scheduling case.png>) |
| 9 | Planned resource profiles for the industrial scheduling case | [Figure 9](<figures/figure 9 - Planned resource profiles for the industrial scheduling case.png>) |
| D1 | Observed acceptance rates of neighborhood operators under adaptive search. | [Figure D1](<figures/figure D1 - Observed acceptance rates of neighborhood operators under adaptive search..png>) |
| D2 | Planned resource profiles for a multi-segment industrial instance | [Figure D2](<figures/figure D2 - Planned resource profiles for a multi-segment industrial instance.png>) |
