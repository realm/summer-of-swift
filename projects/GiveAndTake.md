# Give and Take

## Description

Pre-iOS 8, many apps synced their documents to Dropbox to enable their
documents to be accessed in other apps. iOS 8 allows such apps to
access Dropbox-like services through Document Provider extensions.

This Swift project aims to provide examples of:

  1. A document provider extension
  2. An app accessing documents from a document provider extension

[Apple's example][NewBox] is not adequately illuminating (and is not in
Swift :)), hence this effort.

PRs are welcome. Also welcome are discoveries about iOS 8 document
picker design and usage.

[NewBox]: https://developer.apple.com/library/prerelease/ios/samplecode/NewBox/

## Project Location

<https://github.com/roop/GiveandTake>

## Team Members

- Roopesh Chander
  - Github: [@roop](https://github.com/roop/)
  - Twitter: [@roopeshchander](https://twitter.com/roopeshchander)

## Updates

### July 14

  - Plan is to make two apps:
    1. **Give app:**
       - This will become the app that includes a document provider extension
       - Now has a minimal UI for creating simple text files to be exposed
         through the extension
    2. **Take app:**
       - This will be the app that invokes UIDocumentPickerViewController to
         open a document from Give
       - Work on this app has not started yet

### July 25

### August 10

### August 25

### September 10

### September 25
