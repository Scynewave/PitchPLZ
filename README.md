# PitchPLZ

> The original unoriginal amazon product pitching game

## About

PitchPLZ (Pitch Please!) is a twitch community game designed by Biocow & Scynewave Engineering for the [Great Night Podcast](https://www.twitch.tv/nightattack). You can best describe it as a shopping channel-themed reverse charades-style improv game. The rules are simple:

- One player is selected each round as the Salesperson." This player _can not_ see or explicitly hear what the item is.
- The remaining players and/or the host/guest/show producer/etc. all act as "callers" calling into a fictional shopping network. They, as well as the viewers of the show, can see what the item is but must not explicitly tell the current salesperson.
- In each round, an item is selected from a curated list of weird and unexpected amazon items and the Callers pretend to call in and ask _very_ vague questions about the current showcase.
- The viewers visit a voting website to either "Buy" or "Pass" on the item, the results are shown in real-time to the stream and players.
- At the end of the round if there are more "Buys" than "Passes" the salesperson gets a point.
- The player with the most points at the end of the game wins.

## The Overlay App

The overlay app is the "gameboard" for PitchPLZ. It is designed to show Home Shopping Channel-style graphics over the stream, displaying the current item and the count of votes. Data is stored within a [Supabase](https://supabase.com/) database that is designed to easily scale to support tons of concurrent reads/writes, combatting the issue experienced the first time we attempted to play the game on stream. To further harden the app against bursts of traffic we containerize the front end using docker so that it can run through Google Cloud Run.

### Pages

The App has 3 pages that run the system:

- The Vote page is the stream viewer interface. It auto-updates to show if there is an active vote and if so provides a login-free interface to quickly vote "Buy" or "Pass."
- The Overlay Page is an OBS-optimized web source that auto updates with the current item, current vote count, and a funny producer comment area for injecting even more comedy and commentary into the scene.
- The Admin page is the game control panel. This displays the full list of items so something can be selected for the next round. The item can be hidden or displayed in real-time from this page and new items can be added. This page is also where votes are started, stopped, and cleared. As this is a secure area that fully controls the game, a secret token needs to be set and entered each time to access this page.

### Deployment

- Deploy a new Supabase project (hosted or local)
- Init the database with the following Query:

```pgsql
SET statement_timeout = 0;
SET lock_timeout = 0;
SET idle_in_transaction_session_timeout = 0;
SET client_encoding = 'UTF8';
SET standard_conforming_strings = on;
SELECT pg_catalog.set_config('search_path', '', false);
SET check_function_bodies = false;
SET client_min_messages = warning;
SET row_security = off;

CREATE EXTENSION IF NOT EXISTS plpgsql WITH SCHEMA pg_catalog;

COMMENT ON EXTENSION plpgsql IS 'PL/pgSQL procedural language';

SET default_tablespace = '';

SET default_with_oids = false;

CREATE TABLE public._products (
    prod_id smallint,
    amzurl character varying(101) DEFAULT NULL::character varying,
    description character varying(124) DEFAULT NULL::character varying,
    imgurl character varying(64) DEFAULT NULL::character varying,
    price numeric(6,2) DEFAULT NULL::numeric,
    active smallint
);

CREATE TABLE public._settings (
    thekey character varying(12) DEFAULT NULL::character varying,
    theval character varying(33) DEFAULT NULL::character varying
);

CREATE TABLE public._votes (
    vote_id character varying(1) DEFAULT NULL::character varying,
    buy_nobuy character varying(1) DEFAULT NULL::character varying,
    ip character varying(1) DEFAULT NULL::character varying,
    other character varying(1) DEFAULT NULL::character varying
);

-- THIS IS WHERE WE WOULD ADD THE DEFAULT _settings TABLE VALUES IF WE HAD THEM FINALIZED AT THIS TIME
```

- Deploy the Docker image with :

```bash
// We have yet to deploy the first image to Docker. This is just a placeholder.

$ docker run --name PitchPLZ -d -p 8080:80 \
    -e supabaseUrl=instance.url.com \
    -e supabaseKey=WorkerKey \
    PitchPLZ:Latest
```

## Other Info

Yeah I have no idea what I'm doing... however here's a...:

### Todo

- Add Twitch user Auth
- Create Session tokens to allow simultaneous games
- Allow Overlay customization (basic CSS var based)\
- ??? Profit?

### Support

I can barely support myself... more documentation coming...
