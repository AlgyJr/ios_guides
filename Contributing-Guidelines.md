## Getting Started

If you want to contribute to the guides, you can [edit the wiki](https://github.com/codepath/ios_guides/wiki) directly. Before starting, we recommend taking a look at this [blog post](https://medium.com/@episod/writing-great-documentation-44d90367115a#.9nimc8n7t) on how to write great technical documentation.

## Guidelines

The guides philosophy is all about cutting down the content keeping the guides **clean and practical**. In particular:

 * **Don't** include long preambles, blocks of text or extensive vocabulary lessons
 * **Do** focus on the **practical** and be to-the-point
 * **Do** include screen captures and images that supplements the content 
 * **Do** discuss third-party open-source libraries that save you tons of time
 * **Do** put the code blocks first. **Always include practical code samples**
 * **Don't** use elaborate examples that take way too much time to understand.
 * **Do** mention the implications and practical conclusions you've made

## Principles

The most important thing is to **save people as much time as possible**. Include the maximum amount of useful code that we can in the smallest amount of space and 90% should be code in most cases. A few key principles to keep in mind:

 * **Start with an image** (if applicable) that shows what will be covered in the guide
   * The [UICollectionView guide](http://guides.codepath.com/ios/Collection-View-Guide) is a good example.
 * Have **short introductions** that explain the reason why the concept matters
 * Always **share references** at the bottom of the guide that cites all sources
 

## Missing Topics

Check the [issues](https://github.com/codepath/ios_guides/issues) for this repository to see what types of contributions would be most impactful. In particular, there is an [issue](https://github.com/codepath/ios_guides/issues/1) that contains the most important missing topics. Also look for items in the guides with the **Needs Attention** mark which indicates the guide needs some love.

## Style Guide

### Organization of a Guide

 * A typical guide can be broken down into the following parts:
    * **Overview** => Brief introduction to the topic and why it is important 
    * **Images** => Include a couple screen captures of the topic (if applicable)
    * **Basic Use Case** => Walk through the steps to get a simple use case working
    * **Additional Use Cases** => Build on top of the basic use case to add additional helpful use cases (i.e. How to handle cell selection in a TableView)
       * Use plain english instead of technical terms when creating additional use cases (i.e. "Excluding certain files from source control" instead of "Create a .gitignore")
    * **References** => Add links to any references used while building the guide 

 * **Don't** include a [doctoc](https://github.com/thlorenz/doctoc) table of contents. The section links are automatically generated when the guide is published.

### Code Snippets

 * For code snippets, use Swift code formatting:

     ```swift
     func helloWorld() {
         print("Hello World!")
     }
     ```
 * Prefer **comments in the code** over text describing the code
 * Make sure to use **spaces (4) over tabs**
 * **Line length** should not exceed 100 characters

### Linking to Other Guides

 * Link to other guides using: http://guides.codepath.com/ios/Understanding-Swift
 * **Don't** link to other guides using: https://github.com/codepath/ios_guides/wiki/Understanding-Swift
