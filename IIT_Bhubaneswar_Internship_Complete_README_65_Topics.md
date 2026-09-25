# IIT Bhubaneswar Internship --- Complete Technical README

## LLM-Based Geospatial Knowledge Inference and Travel Planning

> **Purpose:** This README is a complete interview-study guide for the
> IIT Bhubaneswar internship work discussed in this chat. It covers all
> **55 topics** from the detailed explanation, including data
> collection, scraping, structured data, LLM planning, annotation,
> uncertainty, geospatial reasoning, validation, evaluation, research
> context, interview framing, and limitations.

------------------------------------------------------------------------

## Table of Contents

1.  Project in One Picture
2.  Actual Problem
3.  Grounding
4.  Web Scraping
5.  TripAdvisor
6.  Apify
7.  Why Apify Instead of Writing Everything Yourself
8.  Google Search / Hotkey Automation
9.  Why Automate Google Queries
10. Wikivoyage
11. Why Python for Wikivoyage
12. Data Cleaning
13. Why Structured Data
14. CSV vs XLSX vs JSON vs JSONL
15. Why JSON Was Important
16. Understanding the JSON Structure
17. Prompt Engineering
18. Zero-Shot vs Few-Shot Prompting
19. Parameter-Informed vs Parameter-Free Prompts
20. Human-in-the-Loop
21. What Annotation Meant
22. The POI List
23. Uncertainty-Aware Planning
24. Buffer Time
25. Risk Profiles
26. Historical Mean vs Recent Mean
27. Attraction Duration
28. Crowd / Peak Timing
29. Persona Modeling
30. Spatial Reasoning
31. GTFS
32. Geopy
33. Geodesic Distance Is Not Travel Time
34. OSRM
35. Why Both Geopy and OSRM
36. Constraint Satisfaction
37. Hard vs Soft Constraints
38. Meal-Gap Validation
39. Why Rule-Based Validation
40. Why Not Use Only an LLM
41. LLM-as-a-Judge
42. Hybrid Validation Architecture
43. Evaluation Metrics
44. CPR / HCPR
45. TripTide
46. Complete Internship Pipeline
47. What Exactly Was Your Role?
48. Complete Interview Answer
49. One-Line Explanation of Every Technology
50. Most Important "WHY" Questions
51. Complete Internship Limitations
52. Easy Way to Remember the Whole Internship
53. Important Limitations for Interviews
54. Final Mental Model
55. Core Technical Learning / Final Takeaway

------------------------------------------------------------------------

# 1. Project in One Picture

The internship should not be described simply as a web-scraping project
or simply as an LLM project.

The most accurate high-level description is:

> **A human-in-the-loop, data-centric LLM-based travel-planning and
> evaluation workflow.**

The overall workflow was:

``` text
                         USER TRAVEL REQUIREMENT
                                  |
                                  v
                     +-------------------------+
                     | Query / Constraints     |
                     | Budget                  |
                     | Dates                   |
                     | People                  |
                     | Traveler Type           |
                     +-----------+-------------+
                                 |
                                 v
                     +-------------------------+
                     | Reference Data          |
                     |                         |
                     | TripAdvisor             |
                     | Wikivoyage              |
                     | Flights                 |
                     | Hotels                  |
                     | Events                  |
                     | Transit                |
                     +-----------+-------------+
                                 |
                                 v
                        +----------------+
                        |      LLM       |
                        | Travel Plan    |
                        +-------+--------+
                                |
                                v
                     +-------------------------+
                     | Human Annotation        |
                     | & Refinement            |
                     +-----------+-------------+
                                 |
                                 v
                     +-------------------------+
                     | Timing / Uncertainty    |
                     | Buffer Calculation      |
                     +-----------+-------------+
                                 |
                                 v
                     +-------------------------+
                     | Automated Validation    |
                     |                         |
                     | Budget?                 |
                     | Route?                  |
                     | Meal gaps?              |
                     | Repetition?             |
                     | POI complete?           |
                     | Transport valid?        |
                     +-----------+-------------+
                                 |
                                 v
                         FINAL VALID PLAN
```

The architecture is deliberately hybrid:

-   automated collection provides breadth;
-   structured schemas make information machine-readable;
-   the LLM provides flexible planning;
-   humans resolve ambiguity;
-   deterministic validators enforce explicit rules.

------------------------------------------------------------------------

# 2. Actual Problem

The problem was not simply:

> "Generate a travel itinerary."

It was:

> **Generate and refine a travel itinerary that is grounded in
> real-world reference information and satisfies multiple constraints
> simultaneously.**

A normal LLM can generate fluent text, but fluent text does not
guarantee operational correctness.

For example:

``` text
Flight arrival: 3:00 PM
Lunch:           2:00 PM
```

This is impossible.

Another example:

``` text
Day 1: Baga Beach
Day 2: Baga Beach
```

This violates a no-repetition requirement.

Another:

``` text
Budget = ₹50,000

Flights       = ₹15,000
Hotel         = ₹30,000
Restaurants   = ₹10,000
Total         = ₹55,000
```

This violates the budget.

Another problem is persona mismatch:

``` text
Traveler type: laid-back

Generated plan:
8 attractions per day
```

The system therefore had to consider:

-   user intent;
-   traveler persona;
-   temporal feasibility;
-   geographic feasibility;
-   transportation;
-   budget;
-   accommodation;
-   meal timing;
-   diversity;
-   grounding;
-   hard constraints;
-   structured output requirements.

The internship report describes this as a constrained planning problem
rather than simple text generation.

------------------------------------------------------------------------

# 3. Grounding

## What is grounding?

**Grounding** means constraining an AI system to use supplied/reference
information instead of freely inventing real-world entities.

### Without grounding

``` text
User
 |
 v
LLM
 |
 v
Restaurant A
Restaurant B
Restaurant C
```

There is no guarantee that those entities are present in the project's
reference information.

### With grounding

``` text
Reference Database
 |
 +-- Restaurant A
 +-- Restaurant B
 +-- Restaurant C
 +-- Restaurant D
        |
        v
       LLM
        |
        v
Grounded itinerary
```

The LLM is instructed to use the available reference entities.

## Why was grounding important?

Travel planning contains real-world entities:

-   restaurants;
-   hotels;
-   attractions;
-   airports;
-   flights;
-   events;
-   transit stops.

If an LLM invents an entity, the resulting itinerary can become
unusable.

### Interview definition

> "Grounding means restricting model generation to trusted or supplied
> reference information so that the model does not freely invent travel
> entities."

------------------------------------------------------------------------

# 4. Web Scraping

## What is web scraping?

Web scraping means programmatically extracting information from
webpages.

For example, a webpage may contain:

``` text
Restaurant Name
Rating
Review Count
Cuisine
Location
Description
```

Instead of manually collecting hundreds or thousands of records,
software can extract them automatically.

## Why did the project need scraping?

The travel-planning system needed real-world reference information.

The conceptual pipeline was:

``` text
Web Sources
    |
    v
Data Collection
    |
    v
Raw Data
    |
    v
Cleaning
    |
    v
Structured Reference Dataset
    |
    v
LLM Planning
```

The purpose was to build a reference corpus instead of depending
entirely on the LLM's internal knowledge.

------------------------------------------------------------------------

# 5. TripAdvisor

TripAdvisor was one of the important data sources.

The documented TripCraft data-sourcing workflow states that restaurant
details and attraction details/subcategories were extracted using a
TripAdvisor Apify scraper.

The uploaded datasets contain information such as:

-   restaurant names;
-   ratings;
-   review information;
-   review dates;
-   locations;
-   latitude/longitude-related metadata;
-   websites;
-   contributor/review information.

Conceptually:

``` text
TripAdvisor
     |
     v
Restaurant / Attraction Data
     |
     v
Structured Dataset
     |
     v
Travel Planning Reference Data
```

The purpose was to provide real-world entities that could be used as
grounded information for planning and annotation.

------------------------------------------------------------------------

# 6. Apify

## What is Apify?

Apify is a platform for web scraping and browser automation.

Conceptually:

``` text
Website
   |
   v
Apify Actor
   |
   v
Structured Dataset
```

An Apify actor can handle much of the infrastructure associated with
collecting web information.

## Why was it useful?

Instead of implementing every component from scratch:

``` text
HTTP requests
HTML handling
Pagination
Browser handling
Extraction
Storage
Error handling
```

a managed scraping actor can reduce engineering effort.

### Interview answer

> "We used Apify for the documented TripAdvisor extraction because it
> reduced the amount of custom browser-level scraping infrastructure
> required and provided structured extraction suitable for repeated data
> collection."

------------------------------------------------------------------------

# 7. Why Apify Instead of Writing Everything Yourself?

There were several alternatives.

## Option A --- Requests + BeautifulSoup

``` python
requests.get(url)
BeautifulSoup(...)
```

### Advantages

-   lightweight;
-   simple;
-   full Python control.

### Problems

-   pagination handling;
-   custom parsing;
-   dynamic pages;
-   anti-bot handling;
-   maintenance when HTML changes.

------------------------------------------------------------------------

## Option B --- Selenium

Selenium controls a browser.

``` text
Python
  |
  v
Selenium
  |
  v
Chrome / Browser
  |
  v
Website
```

### Advantages

-   handles browser interactions;
-   useful for dynamic pages.

### Problems

-   slower;
-   browser overhead;
-   more engineering;
-   more difficult to scale.

------------------------------------------------------------------------

## Option C --- Playwright

Similar to Selenium but modern and powerful for browser automation.

Useful when browser rendering or interaction is required.

------------------------------------------------------------------------

## Option D --- Official API

If an appropriate official API exists, it is usually more structured and
stable.

Possible limitations:

-   API access;
-   quotas;
-   cost;
-   coverage restrictions.

------------------------------------------------------------------------

## Why Apify fit this workflow

For the documented TripAdvisor extraction, Apify reduced custom
browser/scraping engineering and provided structured output at scale.

------------------------------------------------------------------------

# 8. Google Search / Hotkey Automation

You described another part of the internship as repetitive restaurant
discovery using browser keyboard/hotkey automation.

The workflow can be understood as:

``` text
Restaurant Query List
       |
       v
Automation
       |
       v
Open / Focus Browser
       |
       v
Execute Google Query
       |
       v
Candidate Restaurant Information
       |
       v
Verification / Collection
```

### Important interview wording

Unless the implementation actually scraped Google result pages, do
**not** describe this as "Google scraping."

A safer and more accurate description is:

> "I automated repetitive browser-based Google queries using
> keyboard/hotkey automation for restaurant discovery."

The source code for this particular workflow was not present in the
uploaded internship artifacts, so implementation-specific details should
only be claimed if you personally remember them.

------------------------------------------------------------------------

# 9. Why Automate Google Queries?

Suppose there are:

``` text
100 cities
x
10 searches per city
=
1,000 repetitive searches
```

Manual execution becomes:

-   slow;
-   repetitive;
-   tedious;
-   more prone to inconsistent execution.

Automation turns:

``` text
Open Google
Type query
Search
Inspect
Repeat
```

into:

``` text
Query list
   |
   v
Automation
   |
   v
Search
   |
   v
Next query
```

## Alternatives

### Manual search

Simple but highly repetitive.

### Search API

More structured and reproducible, but requires API access, quotas and
potentially cost.

### Direct provider/API lookup

More targeted and structured if the required information is available
from a provider.

The browser/hotkey workflow was useful for reducing repetitive
interactive work, but an API would generally be easier to reproduce and
scale reliably.

------------------------------------------------------------------------

# 10. Wikivoyage

Wikivoyage was another important source for attraction and activity
information.

The uploaded outputs include structures such as:

``` text
City
Category
Attraction
Description
```

and:

``` text
City
Activity
Description
```

You described writing a custom Python scraper for Wikivoyage.

The uploaded workbooks verify the resulting structured data, while the
actual scraper source was not among the uploaded artifacts.

Therefore, interview claims about the exact scraping library or parser
should only be made if you remember those implementation details.

------------------------------------------------------------------------

# 11. Why Python for Wikivoyage?

Python is useful for custom web extraction and transformation.

A generic workflow is:

``` text
Wikivoyage Page
      |
      v
HTTP Request / Browser
      |
      v
HTML
      |
      v
Parser
      |
      v
Extract Relevant Fields
      |
      v
Clean / Transform
      |
      v
Pandas / Structured Data
      |
      v
CSV / XLSX / JSON
```

For example:

``` text
Wikivoyage
   |
   +-- See
   |    +-- Museum
   |    +-- Fort
   |    +-- Beach
   |
   +-- Do
        +-- Hiking
        +-- Water activities
```

could become:

``` text
City | Category | Attraction | Description
```

or:

``` text
City | Activity | Description
```

## Alternatives

-   BeautifulSoup/lxml for static HTML;
-   Selenium for browser rendering;
-   Playwright for browser automation;
-   Apify for managed extraction.

A custom Python scraper provides strong control over the final schema
but requires more maintenance.

------------------------------------------------------------------------

# 12. Data Cleaning

Scraped data is not automatically clean.

For example:

``` text
"Restaurant Name "
" restaurant name"
"RESTAURANT NAME"
```

may represent the same entity.

Similarly:

``` text
"4.5/5"
4.5
"4.5"
```

represent different data representations of the same concept.

A cleaning pipeline is therefore required:

``` text
Raw Data
   |
   v
Remove Duplicates
   |
   v
Normalize Names
   |
   v
Normalize Types
   |
   v
Handle Missing Values
   |
   v
Check Geographic Information
   |
   v
Separate Entity Types
   |
   v
Clean Reference Dataset
```

Important principles:

-   normalize field names;
-   normalize data types;
-   preserve exact entity names where downstream validation depends on
    them;
-   separate restaurants, attractions, hotels, events, flights and
    transit;
-   flag incomplete information instead of inventing values;
-   maintain geographic consistency;
-   expose only relevant reference information to downstream planning.

------------------------------------------------------------------------

# 13. Why Structured Data?

Raw webpages are rich but noisy.

Example:

``` text
HTML
HTML
HTML
JavaScript
Navigation
Reviews
Advertisements
Metadata
```

This is inconvenient for downstream planning.

A structured record is much easier:

``` json
{
  "city": "Goa",
  "category": "Beach",
  "attraction": "Baga Beach",
  "description": "..."
}
```

Now software can directly access:

``` python
record["city"]
record["attraction"]
```

Structured data makes the dataset:

-   auditable;
-   machine-readable;
-   easier to validate;
-   easier to search;
-   easier to provide as LLM context.

------------------------------------------------------------------------

# 14. CSV vs XLSX vs JSON vs JSONL

## CSV

Best suited to simple tabular data.

Example:

  City   Restaurant       Rating
  ------ -------------- --------
  Goa    Restaurant A        4.5
  Goa    Restaurant B        4.2

------------------------------------------------------------------------

## XLSX

Useful when humans need to:

-   inspect data;
-   annotate data;
-   filter/sort records;
-   work in spreadsheets.

The internship resources included several XLSX datasets.

------------------------------------------------------------------------

## JSON

Useful for hierarchical information.

Example:

``` json
{
  "trip": {
    "origin": "Delhi",
    "destination": "Goa",
    "days": [
      {
        "day": 1,
        "activities": [
          "Flight",
          "Hotel",
          "Beach"
        ]
      }
    ]
  }
}
```

------------------------------------------------------------------------

## JSONL

JSON Lines stores one JSON object per line.

``` json
{"id":1,"org":"Delhi","dest":"Goa"}
{"id":2,"org":"Mumbai","dest":"Jaipur"}
{"id":3,"org":"Kolkata","dest":"Delhi"}
```

This is useful when every line is an independent training/evaluation
example.

Advantages:

-   easy to stream;
-   independent records;
-   convenient for batch processing;
-   convenient for validation;
-   no need to load one huge JSON document.

------------------------------------------------------------------------

# 15. Why JSON Was Important

Travel plans are hierarchical.

A trip can contain:

``` text
Trip
 |
 +-- User information
 +-- Budget
 +-- Constraints
 +-- Day 1
 |    +-- Breakfast
 |    +-- Attraction
 |    +-- Lunch
 |    +-- Dinner
 |    +-- Accommodation
 |    +-- Event
 |    +-- POI sequence
 |
 +-- Day 2
 |    +-- ...
 |
 +-- Day 3
      +-- ...
```

JSON naturally represents this hierarchy.

It also makes programmatic validation possible.

------------------------------------------------------------------------

# 16. Understanding the JSON Structure

The structured records contain fields such as:

``` text
id
org
dest
days
visiting_city_number
date
people_number
Traveller type
local_constraint
budget
level
Query
plan
```

A simplified example:

``` json
{
  "org": "Delhi",
  "dest": "Goa",
  "days": 5,
  "visiting_city_number": 2,
  "date": ["2026-01-01", "2026-01-05"],
  "people_number": 2,
  "local_constraint": {},
  "budget": 50000,
  "level": "medium"
}
```

The plan contains components such as:

``` text
current_city
transportation
breakfast
attraction
lunch
dinner
accommodation
event
point_of_interest
```

This is important because the validator can inspect each component
independently.

------------------------------------------------------------------------

# 17. Prompt Engineering

Prompt engineering means designing instructions, examples and context so
that an LLM generates the desired output.

A weak prompt might be:

``` text
Plan my trip to Goa.
```

A structured prompt can include:

``` text
Origin
Destination
Number of days
Number of people
Budget
Traveler type
Cuisine preference
Local constraints
Available restaurants
Available attractions
Available accommodation
Transportation information
Required output structure
Timing requirements
```

The more structured prompt gives the model more information with which
to construct the plan.

------------------------------------------------------------------------

# 18. Zero-Shot vs Few-Shot Prompting

## Zero-shot

The model receives instructions without an example.

``` text
Create a 5-day travel itinerary.
```

## Few-shot

The model receives one or more examples.

``` text
Example Input:
Delhi -> Goa
3 days

Example Output:
Day 1: ...
Day 2: ...
Day 3: ...

Now generate a plan for:
Mumbai -> Jaipur
5 days
```

## Why few-shot prompting?

Examples communicate:

-   output structure;
-   level of detail;
-   terminology;
-   ordering;
-   expected formatting.

The TripCraft material uses example-based prompting and specifies
detailed output structure and timing/persona guidance.

------------------------------------------------------------------------

# 19. Parameter-Informed vs Parameter-Free Prompts

A parameter-informed prompt provides structured information such as:

``` text
Traveler type: Adventure
Budget: ₹50,000
Days: 5
People: 2
Destination: Goa
Cuisine: Seafood
```

A parameter-free prompt might simply say:

``` text
Create a Goa itinerary.
```

The parameter-informed version allows the model to personalize the plan.

However, more parameters do not automatically produce a better result.

Too many constraints can conflict.

Example:

``` text
10 attractions
+
low budget
+
minimal travel
+
laid-back traveler
+
no car
+
specific cuisine
```

The planner needs to balance these constraints.

The research material notes that additional parameter information can
improve some continuous objective measures while also increasing some
constraint violations, which is why independent validation remains
important.

------------------------------------------------------------------------

# 20. Human-in-the-Loop

The system was not simply:

``` text
LLM -> Final Answer
```

It was:

``` text
LLM
 |
 v
Human Annotation
 |
 v
Validation
 |
 v
Final Dataset
```

Humans are useful because some decisions are contextual.

For example:

> Should an attraction be moved by 30 minutes?

A program can check whether a time slot exists, but a human may notice
that moving it creates an awkward sequence or conflicts with the
traveler's relaxed preference.

Therefore:

-   rules handle explicit machine-checkable requirements;
-   humans handle ambiguity and contextual trade-offs.

------------------------------------------------------------------------

# 21. What Annotation Meant

Annotation was more than correcting formatting.

A typical annotation process involved:

1.  Read the query.
2.  Understand dates.
3.  Understand budget.
4.  Understand local constraints.
5.  Understand traveler persona.
6.  Preserve grounded entity names.
7.  Check the city route.
8.  Check transportation.
9.  Check meal timings.
10. Check attraction/restaurant repetition.
11. Check POI completeness.
12. Refine timings.
13. Apply uncertainty/buffer guidance.
14. Add remarks explaining important decisions.
15. Re-run validation.

Typical issues:

### Arrival conflict

``` text
Flight lands: 4:00 PM
Restaurant: 2:00 PM
```

### Repetition

``` text
Attraction A
Attraction A
```

### Budget violation

``` text
Actual cost > allowed budget
```

### Transportation violation

``` text
Constraint: No self-driving
Plan: Rental car
```

### Missing POI

An activity appears in a structured field but not in the ordered POI
list.

------------------------------------------------------------------------

# 22. The POI List

POI means **Point of Interest**.

The POI list is an ordered sequence of daily places/activities.

Example:

``` text
09:00 Hotel
   |
   v
10:00 Breakfast
   |
   v
11:00 Museum
   |
   v
14:00 Lunch
   |
   v
16:00 Beach
   |
   v
20:00 Dinner
   |
   v
22:00 Hotel
```

It is important because it represents the chronological sequence of the
day.

The validation process can compare:

-   structured fields;
-   timestamps;
-   POI order;
-   city;
-   transportation;
-   accommodation.

------------------------------------------------------------------------

# 23. Uncertainty-Aware Planning

Real-world travel times are not deterministic.

A naive system may say:

``` text
Airport -> Hotel = 45 minutes
```

But real-world conditions might produce:

``` text
Normal traffic = 45 min
Traffic = 60 min
Heavy traffic = 90 min
```

Therefore, the system used reference statistics and timing buffers
rather than assuming every duration was fixed.

The annotation guidelines describe transportation and
attraction/restaurant Python scripts whose outputs provide reference
buffer statistics.

------------------------------------------------------------------------

# 24. Buffer Time

Suppose:

``` text
Flight arrives = 10:00 AM
Airport exit = 30 min
Travel = 45 min
```

Without extra buffer:

``` text
10:00
  |
10:30
  |
11:15
```

If an additional 20-minute buffer is appropriate:

``` text
10:00
  |
10:30
  |
11:15
  |
+20 min buffer
  |
11:35
```

The point is not to make every activity excessively long.

The point is to represent realistic uncertainty.

------------------------------------------------------------------------

# 25. Risk Profiles

The documented guidelines use three risk profiles:

  Traveler profile                 Buffer range
  ------------------ --------------------------
  Risk tolerant         0.5--1.0 × ideal buffer
  Risk optimized       1.0--1.25 × ideal buffer
  Risk averse          1.25--2.0 × ideal buffer

Example:

If ideal buffer = 20 minutes:

### Risk tolerant

``` text
10–20 minutes
```

### Risk optimized

``` text
20–25 minutes
```

### Risk averse

``` text
25–40 minutes
```

The same itinerary therefore does not necessarily use the same buffer
for every traveler.

------------------------------------------------------------------------

# 26. Historical Mean vs Recent Mean

Suppose reference data provides:

``` text
Overall historical average = 40 minutes
Recent average = 55 minutes
```

The annotator may need to decide which is more relevant.

## Historical mean

Useful when:

-   long-term behavior is stable;
-   recent values are noisy.

## Recent mean

Useful when:

-   current conditions differ from historical conditions;
-   recent information is more relevant.

The documented guidelines allow contextual selection between
overall-data and recent-data means.

The important point is:

> Script output is a reference, not an automatic final decision.

------------------------------------------------------------------------

# 27. Attraction Duration

Different attraction categories have different typical visit durations.

Examples from the documented TripCraft data-sourcing material include:

  Category                      Example average duration
  --------------------------- --------------------------
  Boat Tours & Water Sports                        3.5 h
  Casinos & Gambling                               2.5 h
  Classes & Workshops                              1.5 h
  Concerts & Shows                                 2.5 h
  Food & Drink                                     2.5 h
  Fun & Games                                      1.5 h
  Museums                                          3.0 h
  Nature & Parks                                   4.5 h
  Nightlife                                        2.5 h
  Outdoor Activities                               4.0 h
  Shopping                                         1.5 h
  Sights & Landmarks                               3.0 h
  Spas & Wellness                                  2.0 h
  Water & Amusement Parks                          5.0 h
  Zoos & Aquariums                                 2.5 h

These category averages are useful when an individual attraction does
not have a predefined duration.

The purpose is to prevent unrealistic schedules such as:

``` text
Museum visit = 15 minutes
```

when the category's reference duration is substantially longer.

------------------------------------------------------------------------

# 28. Crowd / Peak Timing

Restaurants and attractions can have peak periods.

Example:

``` text
Restaurant peak:
12:00 PM - 2:00 PM
```

If possible, the itinerary can use:

``` text
11:30 AM
```

or:

``` text
2:30 PM
```

instead.

However, the guidelines allow partial overlap with peak periods when
necessary.

The goal is not to force every activity outside peak hours; it is to
improve realism where feasible.

------------------------------------------------------------------------

# 29. Persona Modeling

The planner considers multiple traveler dimensions.

## Traveler type

Examples:

-   laid-back;
-   adventure-oriented.

This affects activity density and pace.

## Purpose

Examples:

-   relaxation;
-   adventure;
-   culture;
-   nature.

This affects attraction selection.

## Spending preference

Examples:

-   economical;
-   luxury.

This affects accommodation and restaurant choices.

## Location preference

Examples:

-   beach;
-   mountain;
-   city;
-   wildlife.

This affects destination activities.

## Risk tolerance

Affects timing buffers.

A simplified model is:

``` text
Traveler Profile
       |
       +-- Type
       +-- Purpose
       +-- Spending
       +-- Location
       +-- Risk tolerance
                |
                v
        Personalized itinerary
```

------------------------------------------------------------------------

# 30. Spatial Reasoning

Travel planning is a spatial problem.

A bad itinerary might be:

``` text
Hotel
 |
 v
Beach
 |
 v
Museum
 |
 v
Beach
 |
 v
Restaurant
```

with large unnecessary travel.

A better sequence may group nearby places:

``` text
Hotel
 |
 v
Museum
 |
 v
Nearby Restaurant
 |
 v
Nearby Attraction
 |
 v
Hotel
```

The purpose of spatial reasoning is to improve geographic realism and
reduce unnecessary movement.

------------------------------------------------------------------------

# 31. GTFS

GTFS stands for **General Transit Feed Specification**.

It is a standardized format for public transportation data.

It can contain:

-   transit routes;
-   stops;
-   schedules;
-   trips;
-   stop times.

Conceptually:

``` text
POI
 |
 v
Nearest Transit Stop
 |
 v
Transit Route
 |
 v
Schedule
 |
 v
Next Destination
```

The documented pipeline used GTFS schedules for public-transit
information.

------------------------------------------------------------------------

# 32. Geopy

Geopy is a Python geospatial library.

In this workflow, it can be used to calculate geographic proximity
between a POI and transit stops.

Example:

``` text
Museum
 |
 +-- Stop A = 0.5 km
 +-- Stop B = 1.2 km
 +-- Stop C = 2.1 km
```

The nearest stop is:

``` text
Stop A
```

This is useful for connecting POIs to transit information.

------------------------------------------------------------------------

# 33. Geodesic Distance Is Not Travel Time

This is a very important interview concept.

Suppose:

``` text
Hotel -------- Museum
     2 km straight-line
```

This does not mean travel takes a fixed amount of time.

The actual road may be:

``` text
5 km
```

and traffic may make the trip:

``` text
20 minutes
```

Therefore:

-   geodesic distance = geographic proximity;
-   road-network distance = actual route length;
-   travel time = depends on route, mode and conditions.

Geopy/geodesic distance is therefore useful for proximity, not exact
travel time.

------------------------------------------------------------------------

# 34. OSRM

OSRM stands for **Open Source Routing Machine**.

It calculates routes over road networks.

Conceptually:

``` text
Location A
    |
    v
   OSRM
    |
    v
Road Network
    |
    v
Location B
```

Instead of only calculating:

``` text
Straight-line distance = 10 km
```

OSRM can determine the road-network route, which may be:

``` text
Road distance = 14.7 km
```

The documented workflow uses OSRM for pairwise road-network distances.

------------------------------------------------------------------------

# 35. Why Both Geopy and OSRM?

They solve different problems.

### Geopy

Question:

> "Which transit stop is geographically closest?"

``` text
POI
 |
 +-- Stop A: 0.5 km
 +-- Stop B: 1.5 km
```

### OSRM

Question:

> "How far is the road-network route?"

``` text
Location A
   |
   v
OSRM
   |
   v
Road route
   |
   v
Location B
```

Therefore:

``` text
POI
 |
 +---- Geopy -> nearest transit stop
 |
 +---- OSRM -> road-network distance
```

------------------------------------------------------------------------

# 36. Constraint Satisfaction

The itinerary needs to satisfy multiple requirements simultaneously.

Conceptually:

``` text
                 ITINERARY
                     |
        +------------+------------+
        |            |            |
        v            v            v
      Budget        Time        Route
        |            |            |
        v            v            v
     Cuisine       Meals     Transportation
        |            |            |
        +------------+------------+
                     |
                     v
                VALID PLAN
```

Documented constraints include:

-   grounding;
-   complete information;
-   POI completeness;
-   route consistency;
-   diversity;
-   meal gaps;
-   transportation;
-   budget;
-   cuisine;
-   room rules/type;
-   facilities/services.

------------------------------------------------------------------------

# 37. Hard vs Soft Constraints

## Hard constraint

A requirement that must be satisfied.

Example:

``` text
Budget <= ₹50,000
```

If:

``` text
Total = ₹55,000
```

the plan violates the constraint.

Other hard constraints can include:

-   required cuisine;
-   room type;
-   transportation restriction;
-   required facilities;
-   budget.

## Soft preference

A preference that is desirable but may have to be traded off.

Example:

``` text
Traveler prefers beaches.
```

The system may still use another activity if other constraints make it
necessary.

The distinction is important because real-world planning is often a
constrained optimization/trade-off problem rather than a simple
checklist.

------------------------------------------------------------------------

# 38. Meal-Gap Validation

The documented validator requires at least four hours between the end of
one meal and the start of the next.

Example:

``` text
Breakfast: 09:00 - 09:45
Lunch:     13:30 - 14:30
```

Gap:

``` text
13:30 - 09:45 = 3 hours 45 minutes
```

This violates the documented 4-hour meal-gap rule.

Another example:

``` text
Breakfast: 09:00 - 09:45
Lunch:     14:00 - 15:00
```

Gap:

``` text
14:00 - 09:45 = 4 hours 15 minutes
```

This satisfies the documented rule.

This is an excellent example of something that should be checked
deterministically rather than left to an LLM.

------------------------------------------------------------------------

# 39. Why Rule-Based Validation?

Many requirements are binary and machine-checkable.

Examples:

``` python
budget <= allowed_budget
```

or:

``` python
meal_gap >= 4_hours
```

or:

``` python
restaurant not repeated
```

or:

``` python
entity exists in reference database
```

Rules are:

-   deterministic;
-   reproducible;
-   explainable;
-   fast.

Examples of rule-based checks:

-   budget;
-   meal gaps;
-   repetition;
-   route fields;
-   schema;
-   POI completeness;
-   entity existence.

------------------------------------------------------------------------

# 40. Why Not Use Only an LLM?

An LLM may produce:

> "The itinerary looks reasonable."

But a deterministic check can detect:

``` text
Flight arrival = 6:00 PM
Dinner = 5:00 PM
```

LLMs can also:

-   hallucinate;
-   misunderstand exact numerical constraints;
-   produce inconsistent timestamps;
-   miss duplicate entities;
-   make arithmetic mistakes.

Therefore:

``` text
LLM
+
Grounding
+
Human Review
+
Deterministic Validation
```

is safer than:

``` text
LLM only
```

The uploaded report explicitly emphasizes this hybrid approach.

------------------------------------------------------------------------

# 41. LLM-as-a-Judge

Some quality dimensions are difficult to express as exact rules.

Example:

> "Is this itinerary appropriate for a relaxed traveler?"

A deterministic rule cannot easily capture this.

An LLM can compare:

``` text
Traveler profile
      +
Generated itinerary
      |
      v
Semantic evaluation
```

However, an LLM judge can itself be inconsistent and can introduce cost.

Therefore, its best role is complementary.

A practical architecture is:

``` text
Deterministic rules
       +
LLM semantic evaluation
       +
Human review
```

Each component handles a different class of problems.

------------------------------------------------------------------------

# 42. Hybrid Validation Architecture

The full validation architecture can be represented as:

``` text
                 GENERATED PLAN
                       |
                       v
              +------------------+
              | Rule Validator   |
              +--------+---------+
                       |
        +--------------+--------------+
        |              |              |
        v              v              v
      Budget         Timing         Schema
      Route          Meals          POI
      Transport      Repetition     Grounding
        |              |              |
        +--------------+--------------+
                       |
                       v
                Human Annotation
                       |
                       v
               LLM Semantic Review
                       |
                       v
                 FINAL DATASET
```

### Division of responsibilities

  Component             Best suited for
  --------------------- --------------------------------
  Deterministic rules   Exact conditions
  Human                 Ambiguous/contextual decisions
  LLM judge             Semantic quality
  Reference data        Grounding
  Python scripts        Timing/statistics/processing

This is the core strength of the hybrid architecture.

------------------------------------------------------------------------

# 43. Evaluation Metrics

Binary pass/fail is sometimes insufficient.

Consider two plans:

### Plan A

``` text
Museum: 10 AM
Lunch: 1 PM
Beach: 3 PM
Dinner: 8 PM
```

### Plan B

``` text
Museum: 10 AM
Lunch: 12 PM
Beach: 12:30 PM
Dinner: 8 PM
```

Both might satisfy some basic checks, but their quality differs.

The TripCraft evaluation framework therefore uses more detailed
dimensions:

  Metric                      Purpose
  --------------------------- -------------------------------------
  Temporal Meal Score         Naturalness of meal timing
  Temporal Attraction Score   Temporal fit of attraction visits
  Spatial Score               Spatial efficiency/realism
  Persona Score               Alignment with traveler preferences
  Ordering Score              Quality of activity sequence
  CPR                         Commonsense constraint pass
  HCPR                        Hard-constraint pass
  Final Pass / Delivery       Overall successful plan

The important idea is:

> A plan can be valid while still being better or worse in degree of
> temporal, spatial, persona and ordering quality.

------------------------------------------------------------------------

# 44. CPR / HCPR

## CPR

CPR represents commonsense/constraint-oriented pass evaluation.

It can cover things such as:

-   reasonable route;
-   complete information;
-   sensible meal scheduling;
-   valid POI list;
-   non-conflicting transportation.

## HCPR

HCPR focuses on hard constraints.

Examples:

-   budget;
-   room requirements;
-   required cuisine;
-   transportation restrictions;
-   hard attraction/event requirements.

These complement continuous quality metrics.

------------------------------------------------------------------------

# 45. TripTide

TripTide is part of the research context and extends travel planning to
disruption-aware scenarios.

Examples of disruptions:

``` text
Flight cancelled
Hotel unavailable
Restaurant closed
Attraction unavailable
Transportation disruption
```

Original plan:

``` text
Hotel
 |
 v
Restaurant
 |
 v
Attraction
```

Disruption:

``` text
Restaurant unavailable
```

The planning system needs to revise the plan.

The research context describes:

-   human selection/revision of disruption scenarios;
-   automated verification;
-   grounding checks;
-   temporal conflict checks;
-   inter-city transition checks;
-   budget checks;
-   structural checks.

### Important interview distinction

Unless you personally implemented the complete TripTide components,
describe TripTide as:

> "Research context I studied"

rather than:

> "I implemented the entire TripTide system."

------------------------------------------------------------------------

# 46. Complete Internship Pipeline

This is the most useful diagram to memorize.

``` text
                    +----------------------+
                    | Travel Web Sources   |
                    +----------+-----------+
                               |
               +---------------+---------------+
               |                               |
               v                               v
        +--------------+                +---------------+
        | TripAdvisor  |                | Wikivoyage    |
        | + Apify      |                | + Python      |
        +------+-------+                +-------+-------+
               |                                |
               +---------------+----------------+
                               |
                               v
                     +--------------------+
                     | Raw Reference Data |
                     +---------+----------+
                               |
                               v
                     +--------------------+
                     | Data Cleaning      |
                     +---------+----------+
                               |
                               v
                  +-------------------------+
                  | CSV / XLSX / JSON/JSONL|
                  +------------+------------+
                               |
                               v
                     +--------------------+
                     | Query Construction |
                     +---------+----------+
                               |
                               v
                     +--------------------+
                     | Prompt Engineering |
                     +---------+----------+
                               |
                               v
                     +--------------------+
                     | LLM Travel Planner |
                     +---------+----------+
                               |
                               v
                     +--------------------+
                     | Human Annotation   |
                     +---------+----------+
                               |
                               v
                     +--------------------+
                     | Uncertainty /      |
                     | Buffer Adjustment  |
                     +---------+----------+
                               |
                               v
                     +--------------------+
                     | Automated Rules    |
                     | & Validation       |
                     +---------+----------+
                               |
                               v
                     +--------------------+
                     | Quality Evaluation |
                     +---------+----------+
                               |
                               v
                       FINAL DATASET
```

Remember:

> **COLLECT → STRUCTURE → GENERATE → ANNOTATE → BUFFER → VALIDATE →
> EVALUATE**

------------------------------------------------------------------------

# 47. What Exactly Was Your Role?

Based on the uploaded resources and the work you described, your
internship can be explained through three major layers.

## Layer 1 --- Data Acquisition

You worked with:

-   TripAdvisor-derived data;
-   Wikivoyage-derived attraction/activity data;
-   restaurant discovery;
-   web information.

The goal was to turn external information into structured reference
datasets.

------------------------------------------------------------------------

## Layer 2 --- Data Annotation / Refinement

You worked with:

-   JSON;
-   JSONL;
-   CSV;
-   XLSX.

You evaluated and refined LLM-generated travel plans.

You checked:

-   route;
-   timing;
-   budget;
-   restaurants;
-   attractions;
-   accommodation;
-   transportation;
-   meals;
-   POI lists;
-   persona;
-   constraints.

------------------------------------------------------------------------

## Layer 3 --- Validation

You worked with:

-   Python;
-   timing statistics;
-   deterministic rules;
-   reference data;
-   human reasoning.

The goal was to identify problems and improve the generated plans.

### Best high-level description

> "My internship work involved data acquisition, structured travel-plan
> annotation, uncertainty-aware timing, and automated validation within
> an LLM-based geospatial travel-planning workflow."

------------------------------------------------------------------------

# 48. Complete Interview Answer

If the interviewer asks:

## "Tell me about your IIT Bhubaneswar internship."

A strong answer is:

> "At IIT Bhubaneswar, I worked on an LLM-based geospatial
> travel-planning project. The objective was to build reliable
> structured travel-planning data rather than relying on free-form LLM
> generation.
>
> My work involved three major areas: data acquisition, itinerary
> annotation and validation.
>
> For data acquisition, I worked with TripAdvisor-derived restaurant and
> attraction data, where Apify was used for structured extraction. I
> also worked with Wikivoyage-derived attraction and activity
> information and automated repetitive restaurant discovery queries
> using browser keyboard automation.
>
> I then worked with structured CSV, XLSX, JSON and JSONL data. The
> travel plans contained information such as origin, destination, dates,
> budget, traveler type, transportation, accommodation, meals,
> attractions, events and an ordered POI list.
>
> The LLM generated travel plans using the available reference
> information. My role was to inspect and refine these plans. I checked
> whether the entities were grounded in the reference data, whether the
> route was logical, whether restaurants and attractions were repeated,
> whether the budget and user constraints were satisfied, and whether
> the POI sequence was complete.
>
> Another important part was uncertainty-aware timing. Instead of
> assuming transportation or activity durations were fixed, Python
> scripts provided reference timing statistics. These were adjusted
> according to the traveler's risk tolerance, with different buffer
> ranges for risk-tolerant, risk-optimized and risk-averse travelers.
>
> Finally, deterministic validation was used for machine-checkable
> conditions such as budget, meal gaps, repetition, schema and POI
> consistency, while human review handled contextual decisions.
>
> So overall, the internship gave me practical experience in web data
> acquisition, Python data processing, structured datasets, LLM
> prompting, grounding, human-in-the-loop annotation, uncertainty
> modeling and rule-based evaluation."

------------------------------------------------------------------------

# 49. One-Line Explanation of Every Technology

  -----------------------------------------------------------------------
  Topic                               Interview explanation
  ----------------------------------- -----------------------------------
  **Python**                          Used for processing,
                                      scraping-related workflows,
                                      timing/uncertainty calculations and
                                      validation

  **Web Scraping**                    Programmatic extraction of web
                                      information

  **Apify**                           Managed scraping/automation
                                      platform

  **TripAdvisor**                     Source of restaurant/attraction
                                      information

  **Wikivoyage**                      Source of attraction/activity
                                      information

  **JSON**                            Hierarchical machine-readable data
                                      format

  **JSONL**                           One independent JSON record per
                                      line

  **CSV**                             Tabular data format

  **XLSX**                            Spreadsheet format useful for human
                                      inspection/annotation

  **LLM**                             Flexible language-based travel
                                      planner

  **Prompt Engineering**              Designing
                                      instructions/examples/context for
                                      desired output

  **Grounding**                       Restricting model generation to
                                      reference information

  **Human-in-the-loop**               Combining human judgment with
                                      automated processing

  **Constraint Satisfaction**         Ensuring required conditions are
                                      simultaneously satisfied

  **Uncertainty Modeling**            Accounting for variability in
                                      travel/activity timing

  **Risk Tolerance**                  Controls timing buffer size

  **POI**                             Ordered daily
                                      Point-of-Interest/activity list

  **GTFS**                            Standardized public-transit
                                      schedule format

  **Geopy**                           Geographic proximity/distance
                                      calculations

  **OSRM**                            Road-network routing/distance
                                      engine

  **Data Validation**                 Programmatic quality and constraint
                                      checking

  **LLM-as-a-Judge**                  LLM used as a complementary
                                      semantic evaluator

  **CPR/HCPR**                        Commonsense/hard-constraint pass
                                      measures

  **Temporal Score**                  Measures timing quality

  **Spatial Score**                   Measures spatial efficiency/realism

  **Persona Score**                   Measures alignment with traveler
                                      preferences

  **Ordering Score**                  Measures activity sequence quality
  -----------------------------------------------------------------------

------------------------------------------------------------------------

# 50. Most Important "WHY" Questions

## Why scraping?

Because the planner needed grounded real-world travel entities.

## Why Apify?

To reduce custom scraping/browser engineering for the documented
TripAdvisor extraction.

## Why Python?

For flexible processing, custom extraction workflows, timing
calculations and validation.

## Why JSON?

Travel plans are hierarchical and JSON naturally represents nested
structures.

## Why JSONL?

Each travel example can be independently stored, streamed and validated.

## Why LLM?

LLMs provide flexible planning and personalization.

## Why not LLM only?

LLMs can generate fluent but temporally, spatially or factually invalid
plans.

## Why human annotation?

Humans can resolve ambiguous contextual decisions and explain
trade-offs.

## Why rule-based validation?

Exact constraints are better handled deterministically.

## Why GTFS?

It provides standardized public-transit schedule information.

## Why Geopy?

For geographic proximity calculations such as finding nearby transit
stops.

## Why OSRM?

For road-network distance/routing instead of only straight-line
distance.

## Why uncertainty modeling?

Real-world transportation and activity duration are variable.

## Why risk profiles?

Different travelers have different tolerance for timing uncertainty.

## Why continuous evaluation?

Pass/fail does not capture degrees of temporal, spatial, persona or
ordering quality.

------------------------------------------------------------------------

# 51. Complete Internship Limitations

Important limitations to understand:

### 1. Web data can become stale

A restaurant, hotel or attraction may change.

### 2. Scraper schemas can change

A website can modify its HTML structure.

### 3. Search automation is less reproducible than an API

Keyboard/browser automation depends more heavily on the interactive
environment.

### 4. Historical buffers are estimates

A historical average is not a guarantee for a specific day.

### 5. Geodesic distance is not travel time

Geographic proximity does not capture traffic or actual road/transit
conditions.

### 6. LLMs can still create temporal/spatial errors

Grounding and validation reduce risk but do not eliminate all errors.

### 7. Hard constraints may conflict with preferences

A perfect match to every preference may not exist.

### 8. Human annotation introduces variability

Different annotators may make slightly different contextual judgments.

### 9. Published research is broader than one intern's implementation

Do not claim every component of TripCraft or TripTide as your personal
implementation unless you actually worked on it.

------------------------------------------------------------------------

# 52. Easy Way to Remember the Whole Internship

Memorize:

> **COLLECT → STRUCTURE → GENERATE → ANNOTATE → BUFFER → VALIDATE →
> EVALUATE**

## COLLECT

TripAdvisor\
Wikivoyage\
Restaurant discovery\
Web sources

↓

## STRUCTURE

CSV\
XLSX\
JSON\
JSONL

↓

## GENERATE

LLM\
Prompt engineering\
Grounding

↓

## ANNOTATE

Human review\
Constraints\
POI\
Persona

↓

## BUFFER

Python\
Historical statistics\
Risk tolerance\
Activity duration

↓

## VALIDATE

Budget\
Timing\
Route\
Meals\
Transport\
Repetition\
Grounding\
Schema

↓

## EVALUATE

Temporal\
Spatial\
Persona\
Ordering\
CPR/HCPR

↓

## FINAL DATASET

------------------------------------------------------------------------

# 53. Important Limitations for Interviews

If asked:

## "What were the limitations?"

Answer:

> "The biggest limitation was that travel data is dynamic. Web
> information can become stale and scraper schemas can change.
> Historical timing buffers are estimates rather than guarantees, and
> geodesic distance does not directly represent travel time. LLMs can
> also still produce temporal or spatial inconsistencies. That's why the
> workflow combined reference data, human review and deterministic
> validation instead of relying on one component."

If asked:

## "Why didn't you trust the LLM?"

Answer:

> "Because fluency doesn't guarantee feasibility. An LLM can produce a
> very convincing itinerary with an impossible timestamp, a repeated
> attraction, an incorrect budget or an unsupported entity.
> Deterministic checks are much more reliable for exact constraints."

If asked:

## "Why human annotation?"

Answer:

> "Rules are excellent for explicit conditions, but humans are better at
> ambiguous contextual decisions, such as whether an itinerary feels too
> dense for a laid-back traveler or whether moving one activity creates
> a better overall sequence."

------------------------------------------------------------------------

# 54. Final Mental Model

Do not think:

``` text
"I scraped some websites and annotated JSON."
```

Think:

``` text
                    REAL WORLD
                        |
                        v
                 WEB DATA SOURCES
                        |
                        v
                 DATA ENGINEERING
                        |
                        v
              STRUCTURED KNOWLEDGE
                        |
                        v
                 +-------------+
                 |     LLM     |
                 |   PLANNING  |
                 +------+------+
                        |
                        v
                  HUMAN REVIEW
                        |
                        v
               UNCERTAINTY MODEL
                        |
                        v
              DETERMINISTIC RULES
                        |
                        v
                QUALITY METRICS
                        |
                        v
                 RELIABLE DATA
```

The project demonstrates an important principle in applied AI:

> **Reliable AI systems require more than a model.**

They need:

-   good reference data;
-   structured representation;
-   grounding;
-   prompting;
-   constraints;
-   uncertainty handling;
-   human review;
-   deterministic validation;
-   evaluation metrics.

------------------------------------------------------------------------

# 55. Core Technical Learning / Final Takeaway

The strongest way to summarize the internship is:

> **A reliable LLM application is not just an LLM. It needs data,
> grounding, structured schemas, constraints, uncertainty handling,
> human feedback and deterministic validation.**

The internship connected several areas:

``` text
                DATA ENGINEERING
                      |
                      v
               WEB DATA / SCRAPING
                      |
                      v
              STRUCTURED DATA
                      |
                      v
                 PYTHON
                      |
                      v
                 LLM / NLP
                      |
                      v
             PROMPT ENGINEERING
                      |
                      v
                 GROUNDING
                      |
                      v
           HUMAN-IN-THE-LOOP
                      |
                      v
          UNCERTAINTY MODELING
                      |
                      v
          GEOSPATIAL REASONING
                      |
                      v
            RULE-BASED VALIDATION
                      |
                      v
               EVALUATION
                      |
                      v
               AI DATASET
```

This is why the internship is relevant to:

-   Data Engineering
-   Applied AI/ML
-   NLP evaluation
-   LLM applications
-   AI agents
-   Data quality
-   Web data acquisition
-   Geospatial systems
-   Human-in-the-loop AI

------------------------------------------------------------------------

# Quick Revision Sheet

## The 7-step pipeline

``` text
COLLECT
   ↓
STRUCTURE
   ↓
GENERATE
   ↓
ANNOTATE
   ↓
BUFFER
   ↓
VALIDATE
   ↓
EVALUATE
```

## The 5 major validation dimensions

``` text
TIME
SPACE
CONSTRAINTS
GROUNDING
PERSONA
```

## The 3 major human/system layers

``` text
AUTOMATION
   +
HUMAN JUDGMENT
   +
DETERMINISTIC VALIDATION
```

## The most important technologies

``` text
Python
Apify
TripAdvisor
Wikivoyage
JSON / JSONL
LLM
Prompt Engineering
GTFS
Geopy
OSRM
```

## The most important concepts

``` text
Grounding
Human-in-the-loop
Constraint Satisfaction
Uncertainty
Risk Tolerance
POI
Rule-Based Validation
LLM-as-a-Judge
Continuous Evaluation
Persona Modeling
Spatial Reasoning
```

------------------------------------------------------------------------

# Source / Scope Note

This README is based on the internship resources and explanations
available in the conversation, including:

-   `TripCraft(2).pdf`
-   `TripTide(2).pdf`
-   `IndianCraft Annotation Guidelines_Interns(1).pdf`
-   `annotator_helper_5_days(1).ipynb`
-   `annotator_helper_7days(1).ipynb`
-   `7_day_wp(1).jsonl`
-   `5_day_ref_info(1).csv`
-   Wikivoyage-derived XLSX files
-   TripAdvisor-derived XLSX/XLS files
-   internship artifact packages
-   the previously generated detailed internship report.

### Important scope distinction

The uploaded material directly supports the documented TripAdvisor/Apify
workflow, structured datasets, annotation process, uncertainty/buffer
guidance, validation rules, geospatial components and research context.

The Google-query/hotkey automation and custom Wikivoyage scraper were
described by the intern, but the corresponding source code was not
present in the uploaded materials. Therefore, implementation-specific
claims about those two components should only be made when the intern
personally remembers the exact implementation.

Likewise, TripCraft and TripTide contain research components broader
than one intern's individual contribution. In interviews, distinguish:

``` text
"I worked on..."
```

from:

``` text
"The research/project included..."
```

This distinction makes the internship explanation technically accurate
and defensible.

------------------------------------------------------------------------

# Final Interview Formula

When explaining any component, use:

``` text
WHAT IS IT?
     ↓
WHY DID WE NEED IT?
     ↓
HOW DID WE USE IT?
     ↓
WHAT WAS THE ALTERNATIVE?
     ↓
WHY DID THIS APPROACH FIT?
     ↓
WHAT LIMITATION DOES IT HAVE?
```

For example:

``` text
Apify
  ↓
Managed web extraction
  ↓
Needed real-world reference data
  ↓
Alternative: custom Selenium/BeautifulSoup
  ↓
Less scraping infrastructure for repeated extraction
  ↓
Depends on actor/schema and website availability
```

This six-step pattern can be applied to almost every technology in the
internship.

------------------------------------------------------------------------

## One-Sentence Final Summary

> **At IIT Bhubaneswar, I worked on a data-centric, human-in-the-loop
> LLM travel-planning workflow in which real-world travel data was
> collected and structured, LLM-generated itineraries were grounded and
> manually refined, timing uncertainty was modeled using reference
> statistics and traveler risk tolerance, and deterministic validation
> plus quality metrics were used to create more reliable travel-planning
> data.**


---

# 56. Research Papers — What They Are About and What They Try to Solve

The two main papers in the internship resources address two stages of LLM travel planning:

```text
TripCraft
   |
   | Can an LLM generate a realistic,
   | personalized, constraint-aware trip?
   v
INITIAL ITINERARY
   |
   | disruption / unexpected event
   v
TripTide
   |
   | Can an LLM revise the itinerary
   | while preserving the user's intent?
   v
ADAPTIVE ITINERARY
```

## 56.1 TripCraft

**Full title:** *TripCraft: A Benchmark for Spatio-Temporally Fine Grained Travel Planning*

TripCraft was published at ACL 2025. It addresses limitations the authors identify in earlier travel-planning benchmarks such as TravelPlanner and TravelPlanner+: semi-synthetic data, spatial inconsistencies, and missing real-world constraints. It introduces a more realistic benchmark incorporating public transit schedules, events, diverse attraction categories and user personas. citeturn0search2turn0search14

### What problem is it trying to solve?

The problem is not simply "Can an LLM write a travel plan?"

It is:

> **Can an LLM generate a detailed itinerary that is realistic in space and time, personalized to the traveler, and consistent with real-world constraints?**

For example, a fluent plan may still contain:

```text
Flight arrives: 4 PM
Lunch:           2 PM
```

or:

```text
Day 1: Beach A
Day 2: Beach A
```

or:

```text
Hotel -> attraction -> restaurant -> attraction
```

with unnecessarily large geographic movement.

TripCraft therefore focuses on **spatio-temporal coherence**, personalization and realistic constraints.

### What does "spatio-temporally fine-grained" mean?

**Spatial:** Where are activities located relative to each other?

**Temporal:** When does each activity happen and how long does it take?

**Fine-grained:** The benchmark considers details such as:

- POI duration;
- transit between POIs;
- meal timing;
- activity ordering;
- public transit;
- events;
- traveler persona.

### What does TripCraft contain?

The paper reports:

- 1,000 travel queries;
- 140 U.S. cities;
- 3-day, 5-day and 7-day itineraries;
- public transit information;
- public events;
- diverse attraction categories;
- user personas;
- human-annotated gold-standard plans.

It reports 25 human annotators performing multiple refinement rounds with detailed remarks. citeturn0search14

### Why human annotation?

The LLM can produce candidate plans, but the benchmark needs high-quality reference plans for evaluation:

```text
Query
  |
  v
Candidate itinerary
  |
  v
Human annotation
  |
  v
Refinement
  |
  v
Reference / gold plan
```

### What does TripCraft add?

1. Real-world constraints
2. Public transit
3. Public events
4. Diverse attractions
5. User personas
6. Fine-grained timing
7. POI-level planning
8. Continuous evaluation

### Why are the evaluation metrics important?

A simple:

```text
PASS / FAIL
```

cannot distinguish all quality differences.

TripCraft therefore introduces five continuous dimensions:

| Metric | What it checks |
|---|---|
| Temporal Meal Score | Quality/naturalness of meal timing |
| Temporal Attraction Score | Temporal fit of attraction visits |
| Spatial Score | Spatial efficiency/coherence |
| Ordering Score | Quality of activity sequence |
| Persona Score | Match with traveler preferences |

The paper specifically presents these as a way to go beyond binary constraint evaluation. citeturn0search2

### Parameter-informed planning

TripCraft compares settings with and without additional parameter information. The paper reports that the parameter-informed setting improved the 7-day Temporal Meal Score from 61% to 80%, a 19-percentage-point gain. citeturn0search2

### TripCraft in one sentence

> **TripCraft asks: "Can an LLM generate a realistic, personalized and spatio-temporally coherent travel itinerary under real-world constraints?"**

---

# 57. TripTide

**Full title:** *TripTide: A Benchmark for Adaptive Travel Planning under Disruptions*

TripTide was published in **Findings of ACL 2026**. citeturn0search1

## What problem is it trying to solve?

TripCraft mainly addresses:

> **Can an LLM generate a good initial itinerary?**

TripTide asks the next real-world question:

> **What happens when something goes wrong after the itinerary has already been created?**

Examples:

```text
Flight cancelled
Weather closure
Transit cancelled
Restaurant closed
Attraction overbooked
Hotel unavailable
```

TripTide evaluates whether an LLM can revise an existing itinerary under these disruptions while preserving the traveler's original intent and respecting practical constraints. citeturn0search1

## Example

Original:

```text
10:00 Museum
13:00 Lunch
15:00 Beach
20:00 Dinner
```

Disruption:

```text
Museum closed
```

A poor system might regenerate the entire trip.

A better system should:

```text
Original plan
     |
     v
Identify disruption
     |
     v
Find affected activity
     |
     v
Find feasible replacement
     |
     v
Preserve user's intent
     |
     v
Check time + space + sequence
     |
     v
Revised plan
```

## Preservation of Intent

Suppose the traveler originally wanted:

```text
Nature + relaxation + seafood
```

If one attraction becomes unavailable, the replacement should ideally preserve those priorities.

**Preservation of Intent** therefore measures how well the revised plan continues to satisfy the original traveler's goals. citeturn0search1turn0search16

## TripTide's main evaluation dimensions

### 1. Preservation of Intent

Does the revised plan preserve the original goals?

### 2. Responsiveness

Does the model react appropriately to the disruption?

### 3. Adaptability

How does the revised plan change semantically, spatially and sequentially?

TripTide also uses LLM-as-a-Judge evaluation and human expert evaluation to assess revision quality. citeturn0search1turn0search16

## What did TripTide find?

The paper reports that LLMs generally preserve semantic intent and sequential structure reasonably well, while spatial deviations can be more pronounced for shorter itineraries and disruption-handling ability decreases as itinerary length increases. citeturn0search1

### TripTide in one sentence

> **TripTide asks: "Can an LLM intelligently adapt an existing travel itinerary when real-world disruptions occur without losing the user's original intent?"**

---

# 58. TripCraft vs TripTide

| Aspect | TripCraft | TripTide |
|---|---|---|
| Main question | Can the LLM generate a good trip? | Can it adapt a trip when something goes wrong? |
| Main problem | Static itinerary generation | Dynamic itinerary revision |
| Key requirement | Spatio-temporal coherence | Adaptation under disruption |
| Personas | Yes | Yes |
| Constraints | Yes | Yes |
| Human evaluation | Yes | Yes |
| Main metrics | Temporal, spatial, ordering, persona | Intent, responsiveness, adaptability |
| Typical failure | Unrealistic initial itinerary | Poor revision after disruption |

### Easiest way to remember

> **TripCraft = Generate**

> **TripTide = Adapt**

---

# 59. How the Two Papers Relate to Your Internship

The internship resources connect strongly to TripCraft through:

- TripAdvisor data;
- Wikivoyage-derived attractions/activities;
- restaurants;
- JSON/JSONL plans;
- prompts;
- personas;
- constraints;
- POI sequences;
- human annotation;
- timing;
- transportation;
- GTFS;
- Geopy;
- OSRM;
- evaluation metrics.

TripTide is primarily research context for the disruption-aware extension:

- itinerary revision;
- disruptions;
- automated validation;
- human evaluation;
- LLM-as-a-Judge;
- preservation of intent;
- adaptive planning.

**Important interview distinction:** unless you personally implemented the disruption-generation and evaluation components, describe TripTide as research context rather than claiming the entire TripTide system as your own implementation.

---

# 60. Research Gap — Interview Answer

## TripCraft

> "Earlier travel-planning benchmarks did not sufficiently represent real-world spatial and temporal constraints, public transit, events, attraction diversity and user personas. TripCraft addresses this by creating a more realistic, fine-grained benchmark and introducing continuous evaluation metrics."

This follows the paper's stated motivation. citeturn0search2

## TripTide

> "Earlier work largely focused on generating an itinerary. TripTide addresses the additional problem of revising an existing itinerary when disruptions occur, while preserving the user's original intent and practical feasibility."

This follows TripTide's stated motivation. citeturn0search1

---

# 61. How to Explain the Papers in an Interview

If asked:

> **"What papers were associated with your internship?"**

Say:

> "The main research paper was TripCraft, which focuses on realistic LLM-based travel planning. It addresses the problem that an itinerary can look fluent but still be spatially or temporally unrealistic, and earlier benchmarks did not fully capture real-world constraints such as public transit, events, attraction diversity and user personas. TripCraft introduces a more detailed benchmark and evaluates plans using temporal meal, temporal attraction, spatial, ordering and persona scores.
>
> The follow-up work is TripTide, which extends the problem from generating an initial itinerary to adapting an existing itinerary when disruptions occur, such as cancellations or closures. It evaluates whether the revised plan preserves the user's original intent, responds appropriately and adapts semantically, spatially and sequentially.
>
> So I remember the research progression as: TripCraft asks whether the model can create a good plan, while TripTide asks whether the model can intelligently modify that plan when reality changes."

---

# 62. Research Scope vs Individual Internship Scope

Be precise about ownership.

### Say this for your work:

> "I worked on..."

### Say this for the paper:

> "The paper proposes..."

### Say this for a project-wide component:

> "The pipeline uses..."

### Avoid saying:

> "I implemented the complete TripCraft/TripTide system."

unless that was actually your responsibility.

This distinction is important because the research papers describe broader datasets, experiments and evaluation frameworks than any one intern necessarily implemented.

---

# 63. Additional Research Context — UTP-Bench

A later 2026 paper, **UTP-Bench: Uncertainty-aware Travel Planning Benchmark**, explores another limitation: travel is stochastic, while many existing benchmarks evaluate relatively deterministic itinerary feasibility.

It focuses on:

- transportation delays;
- crowd fluctuations;
- stochastic timing;
- robustness of itineraries.

It introduces:

- Buffer Adequacy Score (BAS);
- Crowd-Aware Timing Score (CATS);
- Transport Delay Absorption Score (TDAS).

It uses travel data spanning 504 Indian cities. citeturn0academia15

**Important:** This is additional research context, **not part of the original internship work**, unless you independently worked on it.

It connects conceptually to your internship's uncertainty/buffer work:

```text
TripCraft
  -> realistic initial itinerary

TripTide
  -> adapt itinerary after disruption

UTP-Bench
  -> evaluate robustness under uncertainty
```

---

# 64. Final Research Progression

```text
             LLM TRAVEL PLANNING
                     |
                     v
              +-------------+
              |  TripCraft  |
              |             |
              | GENERATE    |
              | realistic   |
              | itinerary   |
              +------+------+ 
                     |
                     v
               INITIAL PLAN
                     |
                     v
             Something changes
                     |
                     v
              +-------------+
              |  TripTide   |
              |             |
              | ADAPT       |
              | itinerary   |
              +------+------+ 
                     |
                     v
              REVISED PLAN
                     |
                     v
              +-------------+
              | UTP-Bench   |
              |             |
              | ROBUSTNESS  |
              | under       |
              | uncertainty |
              +-------------+
```

### Memorize:

> **TripCraft = Generate**

> **TripTide = Adapt**

> **UTP-Bench = Robustness**

---

# 65. Final One-Paragraph Research Summary

> **The research progression moves LLM travel planning from static generation toward reliable real-world decision making. TripCraft focuses on generating personalized, spatio-temporally coherent itineraries using real-world constraints, transit, events, attraction information and personas. TripTide extends this to dynamic situations where disruptions occur and asks whether an LLM can revise an itinerary while preserving user intent and practical feasibility. Later uncertainty-aware work such as UTP-Bench explores whether itineraries remain robust when travel times, crowds and transportation conditions are stochastic. My internship work fits into this broader data and evaluation pipeline through web-data acquisition, structured travel information, human annotation, uncertainty-aware timing and validation.**
