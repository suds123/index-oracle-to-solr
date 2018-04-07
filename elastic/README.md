## Terms lookup mechanism

When it’s needed to specify a terms filter with a lot of terms it can be beneficial to fetch those term values from a document in an index. A concrete example would be to filter tweets tweeted by your followers. Potentially the amount of user ids specified in the terms filter can be a lot. In this scenario it makes sense to use the terms filter’s terms lookup mechanism.

The terms lookup mechanism supports the following options:

index

The index to fetch the term values from.

type

The type to fetch the term values from.

id

The id of the document to fetch the term values from.

path

The field specified as path to fetch the actual values for the terms filter.

routing

A custom routing value to be used when retrieving the external terms doc.
