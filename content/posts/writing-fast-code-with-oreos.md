---
title: "Writing Fast Code With Oreos (OEOs)"
date: 2020-12-02
tags: ["performance", "optimization", "django", "python"]
draft: false
---

## The Problem

You are tasked with making a classic shopping cart system. You have been assured that this system will
need to be extremely fast, as it will be used for millions of transactions in the next year. How do you 
write code that can be easily optimized, and will therefore set yourself and other developers for success
in the upcoming iterations?

In my experience, the difficulty to optimize code is proportional to how well it conforms to distinct ETL
stages, which I like to call OEOs (pronounced like a child saying "Oreo"). We'll talk more on this later;
first, we need to clarify some terminology.

**NOTE**: For simplicity, this article will use a classical database design and an ORM to load and
save the data. There are many other ways to design systems, and the same principles apply.

## ETL

Let's define [ETL][etl] operations for this discussion.

E = Extract. We need to load all relevant data into our system to perform the task, whether its from the
user, database, or anywhere else.

T = Transform. We need to apply our business logic and build our new information.

L = Load. This is where we save our new information into the system.

## Performance Optimization

In my experience with traditional ORM systems, *80% of the work is done through database access optimization*,
which we will focus on in this article. Django has great [documentation][django-db-optimizations] on 
how to do this if you're interested. However, regardless of what ORM you use: if you need to load, save 
or delete multiple DB objects of the same type, why not do it all in a single query? In turn, this will 
reduce your code's [Big-O Notation][big-o-notation].

## Make it Work, Make it Right, Make it Fast

You have likely heard the saying from [Kent Beck][make-it-work]: *Make it work, make it right, make it fast*. This
is an vital concept for any successful programmer.

The first step in writing code is always to make things *actually work*.

After implementing a proof of concept, developers apply common principles to clean my code, especially
when that feature is likely to be altered by others in the future.

In the final "make it fast" stage, developers should implement best practices to prepare for future 
code optimizations. The question is, *what principles should I apply to prepare for future optimizations?*

## Shopping Cart Example

Back to our shopping cart example. Suppose we have written this code to parse and save a user's shopping
cart, but must now "make it fast".

```python
# Parse user info - one order with two products associated with it
order_data = {"order_products": [{"quantity": 1, "product_id": 123}, {"quantity": 3, "product_id": 456}]}

# Build and save order
order = Order(
    name=order_data["name"],
)
order.save()

for record in order_data["order_products"]:
    # Fetch existing product by ID
    product = Product.objects.get(record["product_id"])

    # Build relationship to product
    order_product = OrderProduct(
        order=order,
        quantity=record["quantity"],
        product=product,
    )

    # Save relationship to product
    order_product.save()
```

## Operational Execution Analysis Example

Our code above can be summarized by these sequential operations:

- Parse the user's shopping order info (extract)
- Create order (transform)
- Save order (load)
- Fetch product #1 (extract)
- Create order product #1 (transform)
- Save order product #1 (load)
- Fetch product #2 (extract)
- Create order product #2 (transform)
- Save order product #2 (load)

We will characterize this to `ETLETLETL` (the first letter of each change in operation type). Let's
call this our `Operational Execution Order`, or `OEO` - sounds delicious!

Suppose after profiling our code, we determine that the best optimization is to fetch all 
products in a single query. This would change our algorithm to:

- Parse the user's shopping order info (extract)
- Fetch all products (extract)
- Create order (transform)
- Save order (load)
- Create order product #1 (transform)
- Save order product #1 (load)
- Create order product #2 (transform)
- Save order product #2 (load)

Our OEO is now `ETLTLTL`. Notice how there is now only one `E` because all
the `extract` operations happen in the same section of code, and thus reduced the number of changes
in operation types. Our code optimization made our OEO smaller (but so much tastier)!

Let's do another optimization by saving all order products in a single query. Our algorithm is now:

- Parse the user's shopping order info (extract)
- Fetch all products (extract)
- Create order (transform)
- Save order (load)
- Create order product #1 (transform)
- Create order product #2 (transform)
- Save all order products (load)

Now we have an algorithm of `ETLTL`. Once again, it appears that optimizing our code required us to change
the order of operations and subsequently reduce the length of our OEO - so tasty!
 
Finally, while this is not necessary, let's save the order object at the same time as the order products.

- Parse the user's shopping order info (extract)
- Fetch all products (extract)
- Create order (transform)
- Create order product #1 (transform)
- Create order product #2 (transform)
- Save order (load)
- Save all order products (load)

This code now has an OEO of `ETL`. All `extract` operations occur 
first, followed by `transform`, then `load` - three distinct and mouthwatering layers!

## The Verdict

Let's be clear: it is not necessary to optimize your code on day one! However, if your code is likely
to need optimization in the near future, it is vital to set yourself up for success, and to do this we
must understand how code evolves during performance tuning.

When code is optimized, the order of operations must almost always change, and the order of operations
gravitates towards three distinct stages: extract, then transform, then load. Therefore, the sooner 
we can define our code into these distinct stages, the less we will need to refactor the code,
and therefore save time in the future.

OEO analysis can be used to determine how well we are prescribing two these distinct ETL stages,
and potentially guide us to write better and more future-proof code.

If Oreos are the perfect three-layered cookie, then the perfect three stages of an OEO is
`ETL` (or possibly `ELT`). 

This is the number one lesson I've learned in all my time optimizing code. Let me know what you think!

[make-it-work]: https://wiki.c2.com/?MakeItWorkMakeItRightMakeItFast#:~:text=your%20code%20base.%5D-,Right.,to%20DesignForPerformance%20ahead%20of%20time.
[django-db-optimizations]: https://docs.djangoproject.com/en/3.1/topics/db/optimization/
[big-o-notation]: https://en.wikipedia.org/wiki/Big_O_notation
[etl]: https://en.wikipedia.org/wiki/Extract,_transform,_load
