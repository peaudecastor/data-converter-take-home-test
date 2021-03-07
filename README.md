# Take Home Assignment

In this coding assignment, you will be building a command line tool that will fetch a JSON file from the Internet, transform the fields to a particular specification and allow crude filtering of the data.

## How to approach this

Where the specification is unclear or falls short, you should give reasonable assumptions and design choices, similar to how you would had this been a project you undertook as part of your regular job. When doing this, is it important to thoroughly document your assumptions and design in the README or other relevant documentation artifacts you choose to produce (e.g. code comments or user manual).


## Evaluation

When we receive your submission, the first thing we’ll do is to unpack the code archive or pull the code from a Github if it has been shared. 

The expectation is that, by following the steps in a README we should be able to get the command to run tests and the solution.

After successfully running the tests, we’ll review the code and design. Feel free to use any open source libraries to get the job done, including standards like Docker, docker-compose, etc. Use any language you would like, but assume the evaluator does not have that compiler/runtime to evaluate it.

Completely treat this as if you this was a project for a day job that you had 1-4 hours on, with respect to your choices for dependencies, how to get another developer started on the code base, and trade offs of functionality, vs completion.


## Requirements

This example assumes an implementation in python, but any language will be accepted. Assume that the evaluator will not have any application dependencies, so instructions on how to install them must be given.

```
# example
virtualenv venv
source  venv/bin/activate
pip3 install -r requirements.txt
make test
```

Your program should retrieve a JSON file from the internet with the following structure, where:

1. `data` is an array of objects keyed by `class`
2. `class` is the vulnerability class such as  `injection` or `bad-crypto`. 
3. The `findings` array is a list of objects with the following fields:
    1. `location` - the full reference to the location of the file referencing the source code manager it is (in this case exclusively Github)
    2. `startLineNumber` - the line where the detected vulnerability starts
    3. `endLineNumber` - the line where the detected vulnerability ends
    4. `type` A more specific type for the vulnerability


```json
{
    "data": [{
        "injection": { 
            "findings": [{
                    "location": "github.com/myorg/a/file.py",
                    "startLineNumber": "1",
                    "endLineNumber": "2",
                    "type": "sql-injection"
                },
                {
                    "location": "githu.com/myorg/a/file.py",
                    "startLineNumber": "5",
                    "endLineNumber": "10",
                    "type": "command-injection"
                }
            ]
        },
        "bad-crypto": {
            "findings": [{
                    "location": "github.com/myorg/a/file.py",
                    "startLineNumber": "1",
                    "endLineNumber": "2",
                    "type": "weak-crypto"
                },
                {
                    "location": "github.com/myorg/c/file.py",
                    "startLineNumber": "1",
                    "endLineNumber": "5",
                    "type": "weak-crypto-2"
                }
            ]
        }
    }]
}
```

Your program should allow crude exploration of this data and remapping of some of the fields on the fields: `class`, `type` and `repo`.


```# return all the findings for injections
python cli.py --sourceUrl=https://raw.githubusercontent.com/peaudecastor/data-converter-take-home-test/main/sample.json --class=injection

# output
[
    {
        "repository": "github.com/myorg/a",
        "file": "file.py",
        "startLineNumber": "1",
        "endLineNumber": "2",
        "class": "injection",
        "type": "sql-injection"
    },
    {
        "repository": "github.com/myorg/a",
        "file": "file.py",
        "startLineNumber": "5",
        "end": "10",
        "class": "injection",
        "type": "sql-injection"
    }
]


# return all the findings in the repo /myorg/c
python cli.py --sourceUrl=https://raw.githubusercontent.com/peaudecastor/data-converter-take-home-test/main/sample.json --repo=github.com/myorg/c

# output
[
    {
        "repository": "github.com/myorg/c",
        "file": "file.py",
        "startLineNumber": "1",
        "end": "5",
        "class": "bad-crypto",
        "type": "weak-crypto-2"
    }
]
 ```   


## Bonus

Implement combined criteria, example:

```
python cli.py --sourceUrl=https://raw.githubusercontent.com/peaudecastor/data-converter-take-home-test/main/sample.json --repo=myorg/a --type=command-injection
```
