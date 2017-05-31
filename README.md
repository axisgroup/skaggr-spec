# skaggr-spec
*A JSON specification for Qlik expression syntax*

## Motivation
QlikView and Sense developers are familiar with the Qlik expression syntax for calculating data in Qlik. For example, if we want to sum up a field called Sales, we simply write:
```
sum(Sales)
```

If we want to calculate that same value against the total data set rather than the current selections, we write:
```
sum({1} Sales)
```
If we want to get really crazy, we can add set modifiers to our set expression to produce complicated sets, like:
```
sum({$<Year = {2008,2009}>} Sales)
```

This syntax continues to scale up into more nested structures, faciliting precise control over our calculations. The syntax is notoriously difficult to learn, but it ultimately works well for an expression written by hand by a developer.

However, the creation and management of this syntax is not easily automated. *Enter the **skaggr-spec**.

<br><br>
## The JSON Spec Template
**skaggr-spec** is a specification for defining the components of any Qlik expression via JSOn. With this template in place, we can start to build more tools for working with Qlik expressions, beyond the handcoding method we use today.

Here are the above examples, written according to the skaggr-spec format.

#### Example: Sum of Sales

```
sum(Sales)
```
becomes
```
{
    type: "aggr",
    value: "sum",
    parameters: [
        {
            type: "explicit",
            value: "Sales"
        }
    ]
}
```

While this JSON is harder to write by hand, it is much more easily parsed by code. Therefore, we can use it for automating Qlik expression management.

#### Example: Sum of Sales for the total data set

```
sum({1} Sales)
```
becomes
```
{
    type: "aggr",
    value: "sum",
    set: {
        type: "set-expression",
        components: [
            {
                type: "set-component",
                identifier: "1",
                modifiers: []
            }
        ],
        operators: [""]
    },
    parameters: [
        {
            type: "explicit",
            value: "Sales"
        }
    ]
}
```

#### Example: Sum of Sales for the Years 2008 and 2009
```
sum({$<Year = {2008,2009}>} Sales)
```
becomes
```
{
    type: "aggr",
    value: "sum",
    set: {
        type: "set-expression",
        components: [
            {
                type: "set-component",
                identifier: "$",
                modifiers: [
                    {
                        type: "set-modifier",
                        field: "Year",
                        forcedExclusion: false,
                        operator: "=",
                        selections: [
                            {
                                type: "set-list",
                                value: ["2008","2009"]
                            }
                        ]
                    }
                ]
            }
        ],
        operators: [""]
    },
    parameters: [
        {
            type: "explicit",
            value: "Sales"
        }
    ]
}
```

<br><br>
## Implementations
* [skaggr-parse](https://github.com/axisgroup/skaggr-parse) – a JS parser that takes in a skaggr-spec and outputs a Qlik expression string
* [skaggr.js](https://github.com/axisgroup/skaggr.js) - a JS interface for building and managing Qlik expressions; like jQuery for Qlik syntax 