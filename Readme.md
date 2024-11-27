# Reciyml

Reciyml is an open standard for storing and sharing recipes in YAML format.

It aims to be simple yet powerful, and to be easily readable by both humans and machines.
It is designed to be adaptable to a wide range of recipes, from simple to complex.

## Motivation

I wrote this standart because I wanted a simple way to store and share recipes in a format that is easy to read and write.

I looked at several existing formats, but none of them quite fit my needs. They were either to limited in scope or to verbose.
What do i mean by verbose? Most recipes found online have instructions that are writte as a long text, which is hard to parse by a machine -
and even for humans it can be hard to follow.

I wanted something that breaks down every step in the most detailed jet simple way possible.

Out of this need, Reciyml was born - a standart that models recipes as a collection of steps, each with its own set of instructions and ingredients.

To make this even more flexible, steps itself can have substeps, allowing for complex recipes to be described in a simple way.

As file format I chose YAML, because it is easy to read and write, and is supported by a wide range of programming languages. But the
idea of Reciyml can be implemented in any other file format.

## Getting Started

> [!NOTE]
> The standart is based on a few key concepts:
>
> - A recipe is a collection of steps
> - Each step itself can have multiple sub-steps

### Recipe Steps

The steps follow a simple structure. For simplicity, the example below is omitting some komplex fields.
All fields of a step are optional, but it is recommended to include at least info.
For the full list of available fields, see the [full specification](#full-specification).

```yaml
MY_STEP_NAME: # the name of the step
  img: "URL"

  order: 1 # see the full specification for more info
  info: "My step info" # description of the step

  amount: 1 # how much result this step produces
  unit: "g" # unit of the result

  duration: "1h" # how long the step takes
  waitTime: "1h" # time to wait after the step is done

  preparation: # instructions to execute the step
    - ... # omitted for brevity

  additionalIngredients:
    - ... # omitted for brevity

  ingredients:
    - ... # omitted for brevity

  steps: # substeps
    - ... # omitted for brevity
```

The field `steps` is a list of sub-steps, which follow the same structure as the main steps.
In this way the data-struczture is recursive, allowing for complex recipes to be described.

The main entry point of a recipe is the `steps` field, which is a list of steps.

```yaml
steps:
  MY_STEP_NAME: ...
  MY_OTHER_STEP_NAME: ...
```

### Full Specification

Any Recipe is structured from a list of steps. Every step has multiple fields, some of which are optional.

A step has simple key-value, fields and komplex fields like `preparation`, `ingredients` and `steps`.

#### Step Field

> [!NOTE]
> A step describes an action in a recipe, such as "Mix the ingredients" or "Bake for 30 minutes"
> A step can have multiple substeps, which are described in the `steps` field, such as "Wipp egg whites", "Create a meringue", "Fold in the flour"

|  field                | type                  | description                                        | example                                     |
| --------------------- | --------------------- | -------------------------------------------------- | ------------------------------------------- |
| img                   | string                | URL to an image                                    | "https://example.com/image.jpg"             |
| order                 | integer               | order of the step, see [order field](#order-field) | 1                                           |
| info                  | string                | description of the step                            | "Mix the ingredients"                       |
|  amount               | float                 | how much this step yield                           | 5                                           |
| unit                  | string                | unit of the amount                                 | "Pieces"                                    |
| duration              | date-string           | how long the step takes                            | "10min"                                     |
| waitTime              | date-string           | time to wait after the step is done                | "1h"                                        |
| preparation           | list of `Preparation` | instructions for executing the step                | see [Preperation field](#preperation-field) |
| additionalIngredients | list of `Ingredient`  | ingredients that are used indirektly               | see [Ingredient field](#ingredient-field)   |
| ingredients           | list of `Ingredient`  | ingredients that are used directly                 | see [Ingredient field](#ingredient-field)   |
| steps                 | list of `Step`        | substeps of the step                               | see [Step field](#step-field)               |
| meta  | any     |  additional info  |  `"Some additional info"` |

<details>
<summary>Example</summary>

```yml
steps:
  Make Batter:
    img: "https://example.com/batter.jpg"
    order: 1
    info: "Create the Batter"
    amount: 500
    unit: "g"
    duration: "10min"
    waitTime: "0" # no wait time, the field can be omitted
    preparation:
      - ... # see Preperation field

    additionalIngredients:
      - ... # see Ingredient field

    ingredients:
      - ... # see Ingredient field

    steps: # substeps
      - ...
```

</details>

#### Order Field

The order field is used to specify the order of the steps in the recipe. Per default, the steps are sorted by the order
they are defined in the file. If the order field is present, the steps are sorted by the order field instead.

It can be also used to group steps together, by giving them the same order number. This can be useful
if some steps have to be executed in parallel.

#### Preparation Field

> [!NOTE]
> The preparation field is a list of instructions that describe how to
> execute the step.

| field | type    | description       | example                   |
| ----- | ------- | ----------------- | ------------------------- |
| value |  string |  some information |  `"10"`                   |
| unit  | string  | some information  |  `"min"`                  |
| info  | string  |  what to do       |  `"Mix the ingredients"`  |
| meta  | any     |  additional info  |  `"Some additional info"` |

<details>
<summary>Example</summary>

```yml
preparation:
  - value: "200"
    unit: "Celsius"
    info: "Oven Temperature"

preparation:
  - info: "Sive the flour"
  - info: "Mix everything together"
```

</details>

#### Ingredient Field

> [!NOTE] > `ingredients` as well as `additionalIngredients` are lists of ingredients that are used in the step.
> The ingredients descripe what is needed to execute the step, while the additionalIngredients are used indirectly.

| field       | type   | description                              | example            |
| ----------- | ------ | ---------------------------------------- | ------------------ |
| name        | string | name of the ingredient                   | "Flour"            |
| amount      | float  | how much of the ingredient is needed     | 500                |
| unit        | string | unit of the amount                       | "g"                |
| temperature | float  | temperature of the ingredient in celsius | 20                 |
| info        | string | additional information                   | "Room temperature" |
| meta  | any     |  additional info  |  `"Some additional info"` |

<details>
<summary>Example</summary>

```yml
ingredients:
  - name: "Flour"
    amount: "500"
    unit: "g"
    info: "best use all-purpose flour"
  - name: "Eggs"
    amount: "3"

additionalIngredients:
  - name: "Cooking Oil"
    info: "Used for greasing the pan"
```

</details>
