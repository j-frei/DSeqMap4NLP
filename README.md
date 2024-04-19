# DSeqMap for NLP

This tool handles annotation data for named-enitity-recognition (NER) that support token-wise (discrete sequences) and character-wise (text) representations and can translate between these representations.

### Example
The following script demonstrates some transformation of annotation data.
```python
from dseqmap4nlp import SpacySequenceMapper, LabelLoader, CharSequenceMapper

# Load JSONL
samples = [
    {"text": "Das ist gut.", "label": [
        (0,3, "a"),
        (2,7, "b"),
        (6,8, "b")
    ]}
]

for sample in samples:
    text = sample["text"]
    anns = sample["label"]
    mapper = SpacySequenceMapper(text, nlp="de")
    charmapper = CharSequenceMapper(text=text)

    annotationset = LabelLoader.from_text_spans(anns, mapper)
    print("Overlaps:", annotationset.countOverlaps())

    filtered_spans = annotationset\
        .toDSeqSpans(strategy=["expand"])\
        .withoutOverlaps(strategy="prefer_longest", merge_same_classes=True)
    print("Overlaps:", filtered_spans.countOverlaps())

    print("Sequence:")
    print(filtered_spans.toFormattedSequence(schema="IOB2"))
    print("Previous sequence:")
    try:
        # Should raise an error...
        print(annotationset.toFormattedSequence(schema="IOB2"))
    except Exception as e:
        print("Error raised: " + repr(e))
```