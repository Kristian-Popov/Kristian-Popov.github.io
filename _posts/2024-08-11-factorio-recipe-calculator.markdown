---
layout: post
title:  "Onager's Factorio Recipe Calculator"
date:   2024-08-11 00:00:00 +0200
categories: factorio
---

# Table of Contents
1. [Welcome to blog](#welcome-to-blog)
1. [User stories](#user-stories)
1. [Planner structure](#planner-structure)
   1. [Machine configurator](#machine-configurator)
   1. [Balance table](#balance-table)
   1. [Build guidance](#build-guidance)
   1. [Double recipe matching](#double-recipe-matching)
   1. [Usage examples](#usage-examples)
1. [Comparison with other build planners](#comparison-with-other-build-planners)
   1. [Kirk McDonald's Calculator](#kirk-mcdonalds-calculator)
   1. [FactorioLab](#factoriolab)
   1. [Helmod](#helmod)
1. [Conclusions](#conclusions)

# Welcome to blog

Hello Everyone,

My name is Kristian, welcome to my blog and first post on it. I'm planning to focus on programming on this blog in the future, but for now I'd like to share an interesting tool I developed during my Factorio addiction.

This is another build planner, aimed at making builds easier and making game more interesting, named **Onager's Factorio Recipe Calculator**.

Unlike some other tools, I decided to implement it in a form of spreadsheet. It gives much more flexibility and takes significantly less time to develop, at the same time it fullfils my needs. The fact that this project succeeded makes me think that spreadsheets are powerful tool, automation of many daily tasks can be be in this way without programming. Also spreadsheets are great for prototyping when there's no certainty about algorithm and some trial-and-error is needed.

In fact, a lot of trial-and-error was needed not only when developing formulas for a spreadsheet, but also when using it and planning builds. Factorio is surprisingly flexible game and there are many ways to achieve the same outcome.

# User stories

It was not easy to understand what exactly is needed for build planning in Factorio, but a couple of user stores helped.

1. As a Factorio engineer I need to calculate production and consumption rate of various recipes in a game.

1. As a Factorio engineer I need Recipe Calculator to support different machines present in a game.

1. As a Factorio engineer I need Recipe Calculator to support productivity modules.

1. As a Factorio engineer I need Recipe Calculator to support speed modules.

1. As a Factorio engineer I need to perform rate matching of two recipes in order to optimize complicated builds.

1. As a Factorio enginner I need to plan builds in a way that recipes with multiple products do not deadlock -> Most prominent example is oil processing.

1. As a Factorio engineer I need to integrate additional speed/productivity modifiers into calculations -> Needed for mining, science.

1. As a Factorio engineer I need to have build validation in order to prevent impossible builds -> Impossible builds include too many modules, beacons, also beacons don't affect machines without module slots.

Efficiency modules are missing here, I don't find them very useful in normal freeplay game, they should be great for deathworld, but I did not play deathworld yet.

# Planner structure

Recipe Calculator consists of a couple parts. Each individual part usually represents individual sheet, they can be linked together by using cell links.

## Machine configurator

If you visit "Machine configs" tab, you'll see a small table. Idea is to have one table per recipe, which allows to calculate crafting speed and productivity modifier of a machine. You can select machine type, what modules are inserted in a machine and surrounding beacons. Addtionally additional speed and productivity modifiers should be used for mining and science recipes.

Table will calculate final crafting speed and productivity modifier. Crafting speed is shown in the same manner and in game. Productivity modifier is slightly different, game show it is "+x %", while table shows it as a multiplier (1 if there's no bonus), but it's easy to visually verify if values match.

Assumtion is that modules are the same in the same group of machines, also machines are surrounded with the same count of beacons (this is often non-trivial unfortunately).

## Balance table

This table is inspired by matrices, used internally in Kirk McDonald's calculator (see [essay](http://kirkmcdonald.github.io/posts/calculation.html)).

Rows represent individual recipes. Each ingredient has 3 columns assigned to it. If recipe consumes particular ingredient, put number of ingredients, consumed during a single recipe iteration, into column "I" ("input"). Similar for produced items, use "O" column ("output").

Note that if recipe both consumes and produced items, you have to put numbers into both columns. There are not many recipes like this in vanilla game, namely Kovarex exuranium enrichment process and Coal Liquefaction.

Column "B" calculates production/consumption rate of a given ingredient, taking into account machine count, recipe duration, crafting speed and productivity modifier.

Feel free to copy/paste additional rows and columns if given number is not enough for your build. Remember to copy all three columns if you want to have one more ingredient.

## Build guidance

When using Recipe Calculator, I generally followed approach that each machine should not be starved, in other words previous stage should produce enough items for next stage to be busy 100% of the time.  Exception are recipes with multiple output ingredients. In this case all ingredients, produced by such recipe, must be consumed in order to avoid blocking on any of the ingredients. This means that next stage after such recipe may or may not be starved, which makes estimating their output tricky.

## Double recipe matching

Sometimes you have two recipes and it's hard to guess ratio between them.
Classic example is <span style="color:green">electronic circuits</span>.
When you add modules into the mix, ratios complicate even more, so this tab is to the rescue.
It allows to find three closest ratios for a given pair of recipes.

User stories for this page:

1. As a Factorio engineer I want to calculate ratios of recipes where **input must be fully satisfied**.

1. As a Factorio engineer I want to calculate ratios of recipes where machine wait time is minimized.

Ratios where amount of input items is smaller than needed for machines to work constantly are shown as "-".
It's theoretically possible to take into account such ratios, but it is not currently implemented.

## Usage examples

Two example builds are provided:

* Electric circuits, built on assembly machines 3 with max. productivity and two beacons with speed 3 modules. Iron and copper plates are used as an input. 7 copper cable machines and 6 electronic circuit machines are used.

Note that for this configuration perfect ratio exists but is very inconvenient (15 machines making copper cables for 14 machines making electronic circuits), but there are many ratios with smaller numbers to choose from (e.g. 5:6).

[Electronic circuits spreadsheet example can be downloaded by this link](/assets/Factorio-recipe-calculator/Electronic circuits.ods)

![Image](/assets/Factorio-recipe-calculator/Electronic circuits example.png)

* Plastic, produced from coal. Productivity modules 1 are used. Coal liquefaction is used. Water is heated by coal too.

[Plastic spreadsheet example can be downloaded by this link](/assets/Factorio-recipe-calculator/Plastic.ods)

![Image](/assets/Factorio-recipe-calculator/Coal to plastic example 1.png)

![Image](/assets/Factorio-recipe-calculator/Coal to plastic example 2.png)

# Comparison with other build planners

## Kirk McDonald's Calculator

This widely known tool can be found [here](https://kirkmcdonald.github.io) and was main inspiration for Recipe Calculator (and FactorioLab too). It is implemented as a web application.

This calculator works by calculating resource usage by using optimal recipes. Unfortunately it navigates through recipe graph on its own and doesn't offer much control. Although Factorio recipe graph is mostly directed acyclic graph (DAG), it gives some choice regarding oil (it can be retrieved from crude oil or coal). Also there's no control on "stopping" on particular input resource. Calculator always starts with ores, which is problematic to me. Because of these limitations I have difficulty using it to calculate ratios for compact builds with limited responsibility. Also it supports vanilla game only.

On the other hand, it supports calculating machine power usage, which Recipe Calculator doesn't support (I personally don't need it at a moment).

## FactorioLab

This tool can be found [here](https://factoriolab.github.io). In general it works in similar way to Kirk McDonald's Calculator, but I find interface easier to navigate. Also it supports many modifications for Factorio and also other simulation games, like Satisfactory and Dyson Sphere Program.

Amazing thing is FactorioLab is able to work 100% offline after initial load.

## Helmod

Unlike Kirk McDonald's Calculator and FactorioLab, this tool is implemented as a modification to the game. It loads all recipes from game definitons, so it supports all mods, but not other games.
Helmod gives all freedom to user. User can select what recipes to use, machines and modules used in every recipe. Additionally it calculates power consumption and pollution of every recipe. The price for this flexibility is complex user interface.

![Image](/assets/Factorio-recipe-calculator/Helmod 1.png)

![Image](/assets/Factorio-recipe-calculator/Helmod 2.png)

# Conclusions

Development for Onager's Factorio Recipe Calculator allowed to me learn functioning of Factorio much deeper than before. Playing with planned builds is much more interesting than putting random number of machines and fixing build afterwards.

[Recipe Calculator can be downloaded by this link](/assets/Factorio-recipe-calculator/Factorio recipe calculator.ods)

This project was developed by LibreOffice, should be compatible with all major spreadsheet applications.
Recipe Calculator was not tested on advanced overhaul mods, feedback is very appreciated.
If you see any errors, feel free to contact me.
