## Final Assignment: Robust Journey Planning

** Important -- this is a preview of the final project **

It is available now to give you a sense of what to expect so that you can start working on this project as you do the exercises and homeworks (they are related). It is 80% correct, but we may still change some of the assumptions as we are getting more data.

The final assignment is to be done in **groups of 4 or 5**.

## Important dates

The assignment (clear, well-annotated notebook; report-like), **with a short, 7max, video of your presentation** is due on **Monday May 25th, 12:00 (noon) CET** (note the change of date).

Instead of oral defense as initially planned, we will organize short Q&A discussions of 6mins per group. The Q&A will be scheduled on **Wednesday May 27th, 13:00 - 17:30 CET** - tentatively, actual times to be discussed on a case by
case basis.

## Problem Motivation

Imagine you are a regular user of the public transport system, and you are checking the operator's schedule to meet your friends for a class reunion. The choices are:

1. You could leave in 10mins, and arrive with enough time to spare for gossips before the reunion starts.

2. You could leave now on a different route and arrive just in time for the reunion.

Undoubtedly, if this is the only information available, most of us will opt for option 1.

If we now tell you that option 1 carries a fifty percent chance of missing a connection and be late for the reunion. Whereas, option 2 is almost guaranteed to take you there on time. Would you still consider option 1?

Probably not. However, most public transport applications will insist on the first option. This is because they are programmed to plan routes that offer the shortest travel times, without considering the risk factors.

## Problem Description

In this final project you will build your own public transport route planner to improve on that. You will reuse the SBB dataset (See next section: [Dataset Description](#dataset-description)).

Given a desired departure, or arrival time, your route planner will compute the fastest route between two stops within a provided uncertainty tolerance expressed as interquartiles. For instance, "what route from A to B is the fastest at least Q% of the time if I want to leave from A (resp. arrive at B) at instant T". Note that *uncertainty* is a measure of a route not being feasible within the time computed by the algorithm.

In order to answer this question you will need to:

- Model the public transport infrastructure for your route planning algorithm.
- Build a predictive model using historical arrival/departure time data and potentially other sources of data for your public transport network.
- Implement a robust route planning algorithm using this predictive model.
- Implement a method to test and validate your results.
- Implement a web visualization to demonstrate your method.

Solving this problem accurately can be difficult. You are allowed a few simplifying assumptions:

- There is no penalty for assuming that delays or travel times on the public transport network are uncorrelated with one another. You will get extra credits if you do.
- Once a route is computed, a traveller is expected to follow the planned routes to the end, or until it fails with unknown consequences (e.g. miss a connection). You **do not** need to address the case where travellers are able to defer their decisions and adapt their journey "en route", as more information becomes available. This requires to consider all alternative routes (contingency plans) in the computation of the uncertainty levels, which is more difficult to implement.
- The planner will not need to mitigate the traveller's inconvenience if a plan fails. Two routes with identical travel times under the uncertainty tolerance are equivalent, even if outside this uncertainty tolerance on one route has a much worse outcome than the other.

## Dataset Description

For this project we will use the data published by the Open Data Platform Swiss Public Transport (<https://opentransportdata.swiss>).

You can find the dataset in the following two places.
- On HDFS at the path `/data/sbb/orc`, see homeworks of weeks 3 and 4 for the methods to access it.

- You can download a subset of it using the following links.
    - <https://os.unil.cloud.switch.ch/swift/v1/CFF/2017-09.tar.lzma>
    - <https://os.unil.cloud.switch.ch/swift/v1/CFF/2017-10.tar.lzma>
    - <https://os.unil.cloud.switch.ch/swift/v1/CFF/2017-11.tar.lzma>
    - <https://os.unil.cloud.switch.ch/swift/v1/CFF/2017-12.tar.lzma>
    - <https://os.unil.cloud.switch.ch/swift/v1/CFF/2018-01.tar.lzma>
    - <https://os.unil.cloud.switch.ch/swift/v1/CFF/2018-02.tar.lzma>
    - <https://os.unil.cloud.switch.ch/swift/v1/CFF/2018-03.tar.lzma>
    - <https://os.unil.cloud.switch.ch/swift/v1/CFF/2018-04.tar.lzma>
    - <https://os.unil.cloud.switch.ch/swift/v1/CFF/metadata.tar.gz>

The folder contains the actual data [istdaten](<https://opentransportdata.swiss/en/dataset/istdaten>).

You can also find the station data [BFKOORD_GEO](https://opentransportdata.swiss/en/dataset/bhlist) under HDFS `/data/sbb/stations/bfkoordgeo.csv`.

The [timetable](https://opentransportdata.swiss/en/cookbook/hafas-rohdaten-format-hrdf/#Abgrenzung) is available on the Open Data Plaftorm Swiss Public Transport.

Format: the istdaten dataset is presented a collection of textfiles with fields separated by ';' (semi-colon). There is one file per day.

Unfortunately, the full description from opentransportdata.swiss is only provided in German. You can use an automated translator ([DeepL](<https://www.deepl.com>) seems to provide a better translation at the time of writing) to get more information, but here are the relevant column descriptions:

- `BETRIEBSTAG`: date of the trip
- `FAHRT_BEZEICHNER`: identifies the trip
- `BETREIBER_ABK`, `BETREIBER_NAME`: operator (name will contain the full name, e.g. Schweizerische Bundesbahnen for SBB)
- `PRODUCT_ID`: type of transport, e.g. train, bus
- `LINIEN_ID`: for trains, this is the train number
- `LINIEN_TEXT`,`VERKEHRSMITTEL_TEXT`: for trains, the service type (IC, IR, RE, etc.)
- `ZUSATZFAHRT_TF`: boolean, true if this is an additional trip (not part of the regular schedule)
- `FAELLT_AUS_TF`: boolean, true if this trip failed (cancelled or not completed)
- `HALTESTELLEN_NAME`: name of the stop
- `ANKUNFTSZEIT`: arrival time at the stop according to schedule
- `AN_PROGNOSE`: actual arrival time (when `AN_PROGNOSE_STATUS` is `GESCHAETZT`)
- `AN_PROGNOSE_STATUS`: look only at lines when this is `GESCHAETZT`. This indicates that `AN_PROGNOSE` is the measured time of arrival.
- `ABFAHRTSZEIT`: departure time at the stop according to schedule
- `AB_PROGNOSE`: actual departure time (when `AN_PROGNOSE_STATUS` is `GESCHAETZT`)
- `AB_PROGNOSE_STATUS`: look only at lines when this is `GESCHAETZT`. This indicates that `AB_PROGNOSE` is the measured time of arrival.
- `DURCHFAHRT_TF`: boolean, true if the transport does not stop there

Each line of the file represents a stop and contains arrival and departure times. When the stop is the start or end of a journey, the corresponding columns will be empty (`ANKUNFTSZEIT`/`ABFAHRTSZEIT`).
In some cases, the actual times were not measured so the `AN_PROGNOSE_STATUS`/`AB_PROGNOSE_STATUS` will be empty or set to `PROGNOSE` and `AN_PROGNOSE`/`AB_PROGNOSE` will be empty.

We will use the SBB data limited around the Zurich area. We will focus on all the stops within 10km of the Zurich train station.

## Grading Method

At the end of the term you will provide a 5mins video, in which each member of the project present a part of the project.

After reviewing your videos, we will invite each group for a 10 mins Q&A.

We will give grades based on the code, videos and Q&A.

We will use the following criteria:

1. The clarity and conciseness of the video presentation, code and Q&A
2. The formulation of the problem and its decomposition into smaller tasks
3. The originality of the solution (system design, analytics, visualization)
4. The quality of the solution (system design, analytics and associated implementation) (written: 20 pts, oral: 10 pts)
5. The explanation of the pro's and con's / shortcomings of the proposed solution  (written: 10 pts, oral: 10 pts)

The solution and associated implementation & explanations will be weighted across the different parts as follows:

- Design and method used to model the public transport network: 10%
- Design and method used to create the predictive models: 30%
- Route planning algorithm: 30%
- Validation method: 10%
- Visualization: 20%

## Hints

Before you get started, we offer a few hints:

- Reserve some time to Google-up the state of the art before implementing. There is a substantial amount of work on this topic. Look for *time-dependent*, or *time-varying networks*, *stochastic route planning under uncertainty* and *isochrone maps*. You should also look in the references provided below.
- You should already be acquainted with the data.
However, as you learn more about the state of the art, spend time to better understand your data.
Anticipate what can and cannot be done from what is available to you, and plan your design strategy accordingly. Do not hesitate to complete the proposed data sources with your own if necessary.
- Start small with a simple working solution and improve on it.
In a first version, assume that all trains and buses are always sharp on time.
Focus on creating a sane programming environment that you can use to develop and test your work as it evolves.
Next, work-out the risk-aware solution gradually - start with a simple predictive model and improve it. In addition you can test your algorithm on selected pairs of stops before generalizing to the full public transport network under consideration.

## References

We offer a list of useful references for those of you who want to push it further or learn more about it:

* Adi Botea, Stefano Braghin, "Contingent versus Deterministic Plans in Multi-Modal Journey Planning". ICAPS 2015: 268-272.
* Adi Botea, Evdokia Nikolova, Michele Berlingerio, "Multi-Modal Journey Planning in the Presence of Uncertainty". ICAPS 2013.
* S Gao, I Chabini, "Optimal routing policy problems in stochastic time-dependent networks", Transportation Research Part B: Methodological, 2006.

## FAQ

This section will be updated with the Frequently Asked Questions during the course of this project. Please stay tuned.

### 1 - Q: Do we need to take into account walking times at the connections?
* A: Yes, but since we do not have the details of the platforms at each location, you can use a universal formula to come up with a reasonable walking time.
Note that your solution must allow connections between different modes of transports, such as from bus to tramways.

### 2 - Q: Can we assume statistical independence between the observed delays?
* A: Yes, see simplifying assumptions in **Problem Description**.
You will incur no penalty for assuming that the delay of a given train (or other mode of transport, ...), at a given location and time is
independent of the delays for all other trains, locations, and times. Even if our experience tells us that this is most of the time not the case.
Also, you must assume that you have no real-time delays information at the time you plan your journey, which limits the benefits you could gain by assuming such a dependency. 

### 3 - Q: Can I take advantage of the fact that a connection departs late most of the time to allow a plan that would otherwise not be possible according to the official schedule.
* A: You may discover that you could take advantage of connections that have a high probability of departing late in order to allow a connection.
However, this is not recommended, or it should come with a warning.
Imagine from a user experience perspective, how would you react if you are being proposed a plan in which a connection is supposed to depart before you arrive.
Furthermore, who would you blame if the plan fails: the planner that came up with a theoretically infeasible plan, or the operator who respected their schedule?

