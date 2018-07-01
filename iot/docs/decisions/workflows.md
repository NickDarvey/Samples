# Workflows

Introduce workflows as a distinct application concept to make the building (and extension) of business processes in reaction to events a first-class development experience.

Most of the value of the products comes from these kinds of business processes.
(e.g. a clever algorithm to detect changes, something super configurable, all-the-ML-in-the-world.)
It is also the piece that will change the most across products.
(i.e. the way users and accounts won't change much, but what workflows are executed will.)
They are not so fast moving as to warrant a code-as-data approach, but do warrant being separated from the rest of the business logic.
They need to support stateful operations (like maintaining a rolling average).