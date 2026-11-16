# CRLP datasets

This directory contains the 25 synthetic projects and 10 industrial projects used in the computational experiments for the capacitated resource leveling problem (CRLP). The files are UTF-8 CSVs. List-valued fields use the separators specified below.

## Projects and files

- `synthetic_data/`: five projects at each of 300, 600, 1200, 3600, and 5000 tasks.
  A name such as `J300_1` identifies the first 300-task project.
- `industrial_data/`: projects named `J220`, `J270`, `J340`, `J642`, `J645`,`J765`, `J1424`, `J1817`, `J2166`, and `J6860`. The number after `J` is the task count.
- Each project `P` has `task_data_P.csv`, `relation_data_P.csv`,`fixed_load_P.csv`, and `capacity_and_sharding_P.csv`.
- `dataset_project_statistics.csv` contains one summary record per project.

Read task identifiers as text and use them to join the task and precedence files. 

## Time and resource conventions

The horizon contains 1440 one-minute slots. These files use zero-based indices`0,...,1439`; index `t` corresponds to slot `t + 1` in the paper's notation.
Execution starts and ends are inclusive, so task duration is`end_time - start_run_time + 1`. Task durations and start-time window widths are measured in minutes.

Task demand, fixed load, and resource capacity within each one-minute slot are expressed in **CU·h**. One CU·h represents the use of one compute unit (CU) for one hour, and one CU contains one CPU core. This unit applies to the values in `max_used_cpu`, both types of `cpu_used_detail`, and `agg_max_cpu`.

## Tasks: `task_data_P.csv`

| Field | Meaning |
| --- | --- |
| `task_id` | Unique task identifier within the project. |
| `deadline_time` | Latest allowed completion slot, inclusive; this is a finish deadline, not a latest start. |
| `earliest_start_time` | Earliest allowed start. |
| `max_used_cpu` | Maximum value of the task's resource-demand profile, in CU·h. |
| `start_run_time` | Task start in the supplied initial schedule. |
| `end_time` | Task finish in the supplied initial schedule, inclusive. |
| `cpu_used_detail` | Daily demand profile encoded as `a;b;v` segments separated by `\|`. Value `v`, in CU·h, applies at slots `a` through `b`, inclusive. Unspecified slots have zero demand. |

Extract the daily profile over `start_run_time,...,end_time` to obtain the task's relative execution-demand vector. Moving a task shifts this vector. Its latest allowed start  is  `deadline_time - duration + 1`.

## Precedence: `relation_data_P.csv`

| Field | Meaning |
| --- | --- |
| `parent_task_id` | Immediate predecessor's task identifier. |
| `child_task_id` | Immediate successor's task identifier. |

Both identifiers occur in the project's task file. A task may start only after its predecessor finishes: `start(child) >= start(parent) + duration(parent)`. For the initial schedule, this is equivalent to `start_run_time(child) > end_time(parent)`.

## Fixed workloads: `fixed_load_P.csv`

| Field | Meaning |
| --- | --- |
| `project_name` | Project identifier, matching `P` in the filename. |
| `max_used_cpu` | Maximum fixed resource load over the horizon, in CU·h. |
| `cpu_used_detail` | 1440 semicolon-separated values in CU·h, ordered from slot 0 through 1439; this is the fixed-load profile `g_t`. |

## Capacity and segments: `capacity_and_sharding_P.csv`

| Field | Meaning |
| --- | --- |
| `project_name` | Project identifier, matching `P` in the filename. |
| `agg_max_cpu` | 24 semicolon-separated capacity values in CU·h, one per hour. Each value applies to all 60 slots in that hour; repeat each value 60 times to obtain the capacity profile `r_t`. |
| `agg_sharding_time` | Semicolon-separated intervals such as `00:00:00-06:00:00`. Starts are inclusive and ends exclusive; `24:00:00` is the end of the day. These segments are used for segment-peak costs. |

## Project statistics: `dataset_project_statistics.csv`

| Field | Meaning |
| --- | --- |
| `dataset` | Dataset type: `Synthetic` or `Industrial`. |
| `project_id` | Project identifier, matching the data filenames and `project_name`. |
| `task_count` | Number of tasks, including zero-demand tasks. |
| `precedence_arcs` | Number of parent-child relations. |
| `time_segments` | Number of intervals in `agg_sharding_time`. |
| `avg_task_duration_min` | Mean task duration in minutes. |
| `avg_start_time_window_width_min` | Mean of latest allowed start minus earliest allowed start, in minutes. |
| `avg_task_peak_resource` | Mean task peak demand across all tasks, in CU·h. |
| `avg_resource_capacity_per_minute` | Mean of the 1440 resource-capacity slot values, in CU·h. |
| `avg_fixed_load_per_minute` | Mean of the 1440 fixed-load slot values, in CU·h. |
| `fixed_load_sum` | Sum of fixed-load values across the 1440 slots. |
| `movable_load_sum` | Sum of task-demand values across all tasks and their execution slots. |
| `capacity_sum` | Sum of capacity values across the 1440 slots. |
| `resource_scarcity_index` | `movable_load_sum / (capacity_sum - fixed_load_sum)`; a larger value indicates greater resource scarcity. |

The three `*_sum` fields are sums of the supplied per-slot resource values.
The resource scarcity index is dimensionless.
