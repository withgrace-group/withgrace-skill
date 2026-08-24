---
name: withgrace
description: Use when working with With Grace property records, villas, units, buyer contacts or market figures. Reads and writes through the With Grace connector, which is scoped to the connected account's own records.
---

# With Grace

With Grace is an architecture and construction practice in Bali. This skill
connects an assistant to the With Grace connector so it can read and write the
records belonging to the connected account.

Setup is in [README.md](README.md). Once connected, the tools below are
available. Nothing here works without a connection, so if a tool is missing,
say so rather than guessing at an answer.

## What the records are

| Record | Holds |
| --- | --- |
| `organizations` | The company a set of records belongs to |
| `properties` | A project, such as a villa development |
| `units` | An individual villa or lot, with its price, area, phase and status |
| `contacts` | People who have been in touch about a project |
| `market` | Dated figures about a location or a comparable development |

Units belong to a property. Properties belong to an organization.

## Tools

| Tool | Use |
| --- | --- |
| `list_entities` | The record kinds available |
| `count_records` | How many of each kind exist |
| `list_records` | Read records of one kind, with an optional `limit` |
| `get_record` | Read one record by its id |
| `create_record` | Add one record |
| `update_record` | Change fields on one record |

## How to use it well

**Every figure comes from a record.** Price, area, availability, phase and any
market number live in the rows these tools return. Never estimate one, never
average one into existence, and never carry a figure from an earlier answer.
If a record is missing the field, say the field is not recorded.

**Read before writing.** `list_records` or `get_record` first, so an update
changes what you think it changes. `update_record` reports which fields it
ignored; read that rather than assuming a write landed.

**The connection decides what is visible.** These tools return the connected
account's own records and nothing else. An empty result means there is nothing
there for this account, not that the tools failed.

**Some fields cannot be changed.** A record's id, its creation time and the
organization it belongs to are fixed. Attempting them is ignored rather than
refused, which is why the ignored list is worth reading.

**Ask before creating.** A created record is real to everyone who reads it
afterwards. Confirm the details with the person first.

## Worked example

> Which units are still available at the hillside project, and what do they cost?

1. `list_records` with `entity: "properties"` to find the property and its id
2. `list_records` with `entity: "units"`
3. Filter to that property and to `status: "available"`
4. Report the codes, prices and areas exactly as the records give them

If no unit is available, say so. Do not offer the nearest alternative as
though it were available.

## When something is not there

Say what is missing and stop. "No market figures are recorded for that
location" is a useful answer. An invented figure about a property someone may
buy is not, and it is the failure this skill exists to prevent.
