# ReciYML

![logo](./assets/logo.png)

ReciYML is an open standard for storing and sharing recipes in YAML format.

It is designed to be simple yet powerful, making recipes easily readable by both humans and machines.
ReciYML is adaptable to a wide range of recipes, from the simplest to the most complex.

## Motivation

I created ReciYML because I wanted a straightforward way to store and share recipes in a format that is both easy to read and write.

When exploring existing formats, I found them either too limited in scope or too verbose.

### What’s wrong with “verbose”?

Many recipes online are written as long blocks of text, making them:

- Hard to parse for machines.
- Challenging to follow for humans, especially for complex recipes with multiple steps.

This even applies to some popular recipe formats in JSON or XML, which can be verbose and difficult to read.

### My Solution

ReciYML breaks down recipes into structured steps, each with its own set of:

- Instructions
- Ingredients
- Metadata (e.g., images, duration, and yield).

Each step can have substeps, creating a recursive structure that allows even the most complex recipes to be described simply.

The chosen file format, YAML, is:

- Easy to read and write.
- Supported by many programming languages.
  However, the concept behind ReciYML can be implemented in other formats like JSON or XML.

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

  instructions: # instructions to execute the step
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

ReciYML describes recipes as a recursive collection of steps. Each step contains:

- Basic fields: Key-value pairs.
- Complex fields: Lists of instructions, ingredients, or substeps.

#### Step Field

> [!NOTE]
> A step describes an action in a recipe, such as "Mix the ingredients" or "Bake for 30 minutes"
> A step can have multiple substeps, which are described in the `steps` field, such as "Wipp egg whites", "Create a meringue", "Fold in the flour"

|  field                | type                  | description                                        | example                                     |
| --------------------- | --------------------- | -------------------------------------------------- | ------------------------------------------- |
| img                   | string                | URL to an image                                    | "https://example.com/image.jpg"             |
| order                 | integer               | Prder of the step, see [order field](#order-field) | 1                                           |
| info                  | string                | Description of the step                            | "Mix the ingredients"                       |
|  amount               | float                 | Yiel of the step                                   | 5                                           |
| unit                  | string                | Unit of the amount                                 | "Pieces"                                    |
| duration              | date-string           | Time required for this step                        | "10min"                                     |
| waitTime              | date-string           | Waiting time after the step                        | "1h"                                        |
| instructions          | list of `Instruction` | Instructions for executing the step                | see [Instruction field](#instruction-field) |
| additionalIngredients | list of `Ingredient`  | Directly used ingredients                          | see [Ingredient field](#ingredient-field)   |
| ingredients           | list of `Ingredient`  | Indirectly used ingredients                        | see [Ingredient field](#ingredient-field)   |
| steps                 | list of `Step`        | Substeps                                           | see [Step field](#step-field)               |
| meta                  | any                   | Additional info                                    |  `"Some additional info"`                   |

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
    instruction:
      - ... # see Instruction field

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

| field | type   | description       | example                  |
| ----- | ------ | ----------------- | ------------------------ |
| value | string | Some information  | `"10"`                   |
| unit  | string | Unit of the value | `"min"`                  |
| info  | string | Instruction       | `"Mix the ingredients"`  |
| meta  | any    | Additional info   | `"Some additional info"` |

<details>
<summary>Example</summary>

```yml
instructions:
  - value: "200"
    unit: "Celsius"
    info: "Oven Temperature"

# or

instructions:
  - info: "Sive the flour"
  - info: "Mix everything together"
```

</details>

#### Ingredient Field

> [!NOTE]
> `ingredients` as well as `additionalIngredients` are lists of ingredients that are used in the step.
> The ingredients descripe what is needed to execute the step, while the additionalIngredients are used indirectly.

| field       | type   | description                              | example                   |
| ----------- | ------ | ---------------------------------------- | ------------------------- |
| name        | string | Ingredient name                          | "Flour"                   |
| amount      | float  | Quantity                                 | 500                       |
| unit        | string | Unit of measurement                      | "g"                       |
| temperature | float  | Temperature of the ingredient in celsius | 20                        |
| info        | string | Additional notes                         | "Room temperature"        |
| meta        | any    | Custom metadata                          |  `"Some additional info"` |

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

## License

ReciYML is open-source under the MIT License. Feel free to contribute or suggest improvements!

## Similar Projects

- [Recipe - A Schema.org Type](https://schema.org/Recipe)
- [Cooklang](https://cooklang.org)
- [Open Recipe Format](https://open-recipe-format.readthedocs.io/en/latest/topics/tutorials/walkthrough.html)
