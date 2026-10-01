# Self-Lifted xG: Data Availability

This repository provides data-access information for an abstract submitted to
the **2027 MIT Sloan Sports Analytics Conference Research Papers Competition**:

**Self-Lifted xG: Distinguishing Post-Control Scoring-Probability Change from
xG Overperformance**

The study uses two publicly accessible event-data sources. Provider files are
not copied into this repository; they should be obtained from the official
links below and used under the corresponding provider terms.

## Data overview

| Dataset | Role in the study | Study coverage |
|---|---|---|
| StatsBomb Open Data | Primary validation | Available competitions and seasons in the open-data release |
| Wyscout Soccer Match Event Dataset | Multi-league application | Five European domestic leagues, 2017/18 |

## How `xG_receive` is constructed

Neither StatsBomb nor the public Wyscout dataset reports xG for a receive or
carry-start event. **`xG_receive` is therefore a model-derived quantity, not a
provider field.**

For each qualifying sequence, the receive point is the earliest carry start by
the eventual shooter in an uninterrupted same-player carry-to-shot chain. The
main model estimates the scoring probability associated with that location
using only spatial information available at the receive point:

```text
x coordinate, y coordinate, distance to goal, and shooting angle
```

The resulting value is interpreted operationally as the estimated probability
of scoring if the player shot immediately from the receive location. It is not
a causal estimate, possession value, expected threat, or a measure of off-ball
contribution. Self-Lifted xG is then defined as:

```text
SLxG_i = xG_shot,i - xG_receive,i
```

For StatsBomb, `xG_shot` is the provider's native shot xG. The full-coverage
`xG_receive` estimator is the position-only spatial logistic model (M0). A
five-fold calibrated version (M1) produces virtually identical probabilities
and player rankings. A contextual model using StatsBomb 360 freeze frames (M2)
improves prediction and lowers estimated SLxG magnitude on the available
subset, but 360 coverage is insufficient for the full analysis; M2 is
therefore used only as a diagnostic.

For Wyscout, both terminal-shot probabilities and receive-location estimates
are fitted within the Wyscout source using out-of-fold predictions. The
Wyscout values are therefore source-specific and are not treated as numerically
interchangeable with StatsBomb xG.

## 1. StatsBomb Open Data

### Official access

- **Repository:** [StatsBomb Open Data](https://github.com/hudl/open-data)
- **Data agreement:** [StatsBomb Public Data User Agreement](https://github.com/hudl/open-data/blob/master/LICENSE.pdf)
- **Format documentation:** [`doc/`](https://github.com/hudl/open-data/tree/master/doc)

The repository can be downloaded directly or cloned with Git:

```bash
git clone https://github.com/hudl/open-data.git statsbomb-open-data
```

The relevant directories are:

```text
statsbomb-open-data/data/competitions.json
statsbomb-open-data/data/matches/
statsbomb-open-data/data/events/
statsbomb-open-data/data/three-sixty/
```

### Use in this study

StatsBomb is the primary validation dataset because it provides native shot
xG, explicit event identifiers, related-event links, and carry start/end
locations. The analysis uses:

- `statsbomb_xg` as terminal shot xG;
- same-player carry-to-shot relationships;
- match, competition, season, player, and team identifiers;
- carry and shot coordinates; and
- StatsBomb 360 data as a contextual diagnostic subset.

The frozen analysis contains **88,023 shots** and **40,684 qualifying strict
same-player sequences** after excluding **108 mixed-player chains**. The main
player-level sample contains **484 player-team-competition-season records**
with at least 20 total shots and 20 qualifying sequences.

StatsBomb Open Data does not provide standardized complete-season coverage for
every competition. The study therefore treats StatsBomb cumulative values as
totals over the observed open-data windows rather than directly comparable
full seasons.

### Terms and attribution

Use of the data is governed by the StatsBomb Public Data User Agreement.
Publications based on the data should identify StatsBomb as the source and
follow its logo-attribution requirements. The raw files are therefore linked
from their official repository rather than redistributed here.

## 2. Wyscout Soccer Match Event Dataset

### Official access

- **Dataset:** [Soccer Match Event Dataset on Figshare](https://doi.org/10.6084/m9.figshare.c.4415000)
- **Dataset paper:** [A public data set of spatio-temporal match events in soccer competitions](https://doi.org/10.1038/s41597-019-0247-7)
- **License:** [Creative Commons Attribution 4.0 International](https://creativecommons.org/licenses/by/4.0/)

The Figshare collection provides downloadable archives for events, matches,
players, teams, and related metadata. This study uses the match and event data
for the following complete 2017/18 domestic-league seasons:

- English Premier League;
- Spanish La Liga;
- German Bundesliga;
- Italian Serie A; and
- French Ligue 1.

### Use in this study

These five competitions contain all **1,826 scheduled matches** and **43,040
shots** used in the league-wide application. The main player-level sample
contains **92 player records** under the same minimum thresholds used for
StatsBomb.

Because the public Wyscout release does not provide provider-native shot xG or
StatsBomb-style explicit carry-to-shot links, the application uses:

- source-specific out-of-fold shot probabilities;
- Wyscout event coordinates and shot tags; and
- heuristic same-player continuity links.

Wyscout results are therefore an external application of the measurement
framework, not a direct provider-to-provider comparison of xG values.

### Citation and attribution

The dataset is distributed under CC BY 4.0. Users should cite the Figshare
collection and the accompanying data paper:

> Pappalardo, L., Cintia, P., Rossi, A. et al. A public data set of
> spatio-temporal match events in soccer competitions. Scientific Data 6, 236
> (2019). https://doi.org/10.1038/s41597-019-0247-7

## Repository scope

This repository documents where the study inputs can be obtained and how each
source enters the analysis. It intentionally contains no copied provider data,
derived result tables, figures, code, or file manifests.
