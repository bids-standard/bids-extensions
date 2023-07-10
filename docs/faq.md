# Frequently asked questions

Below you can find some the answsers to some questions frequently asked
during the development of BIDS extensions proposals.

## Can we introduce new entities / metadata to the BIDS specification?

> We need to introduce an entity to distinguish on-device preprocessing methods
> that lead to the raw data, and found entities `proc` and `rec` in the current specification. 
> Can we extend one or the other, or should we instead create a new entity with analogous description?

The rule of thumb is: try to reuse as much as possible existing entities or medata.
If you feel that those are too restrictive, it may be possible to first extend the definitions of those better cover your use case.
If that is sitll not possible then you can introduce new entity or metadata.

See [this issue](https://github.com/bids-standard/bids-specification/issues/1177#issue-1325080702)
for an example of the reasoning that lead to the reuse of an entity for anatomical MRI.

