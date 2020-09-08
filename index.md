# Writing in Markdown 101



## Text Font Formatting
- To make a phrase _italic_ in Markdown, you can surround words with one underscore or one asterisk on each side of the pharse;
- To make a phrase **bold** in Markdown, you can surround words with two asterisks on both sides;
- To make a pharase ***both italics and bold***, you can surround words with three asterisks on both sides.
```markdown
**Bold** and _Italic_ 

*Italic*

***Bold and Italic***
```

And you will see

>**Bold** and _Italic_ 
>
>*Italic*
>
>***Bold and Italic***

## Headers

There are six types of headers, in decreasing sizes:

```markdown
# Header level 1
## Header level 2
### Header level 3
#### Header level 4
##### Header level 5
###### Header level 6
```

And you will see
># Header level 1
>## Header level 2
>### Header level 3
>#### Header level 4
>##### Header level 5
>###### Header level 6

You simply place the same number of hash marks as the size of the header you want.

## Links

There are two different link types. 
- The first link style is called an _inline link_. To create an inline link, you wrap the link text in brackets, and then wrap the link in parenthesis. 
- The other link is called a _reference_ link, where the link is actually a reference to another place in the document. To create a reference link, you wrap the link text in one pair of brackets, and then wrap the reference text in a second pair of brackets. At the bottom of a Markdown documentm the reference texts are defined as proper links to outside websites, in the form of the same tag name wrapped in brackets, followed by a colon, followed by the link.

```markdown
[Yinpu's Webpage](https://yinpuli.github.io/)

[Yinpu's Webpage][my page]


[my page]: https://yinpuli.github.io/

```

And you will see

>[Yinpu's Webpage](https://yinpuli.github.io/)
>
>[Yinpu's Webpage][my page]
>
>
>[my page]: https://yinpuli.github.io/


## Images

The syntax for creating images is nearly the same as for links. Images also have two styles, just like links, and both of them render exactly the same way. The only difference is that the images are prefaced with an exclamation point.
```markdown
![Check this Puppeteer for Github](https://octodex.github.com/puppeteer/)
```

And you will see

![Check this Puppeteer for Github](https://octodex.github.com/puppeteer/)

## Blockquotes

A blockquote is a sentence or paragraph that has been specially formatted to attention. To create a blck quote, all you need to do is preface a line with the _greater than_ caret. You can also place a caret character on each line of the quote. This is particularly useful if your quote spans multiple paragraphs. Notice than even blank lines must contain the caret character to ensure the entire blockquote be grouped together.

## Lists

- Ordered lists: an ordered list is prefaced with numbers.
- Unordered lists/Bullet points: you will preface each item in the list with an asterisk. Each list item also gets its own line. And you could nest the list within another list. All you have to do is to indent each asterisk one space more than the preceding item.

While you could continue to indent and add sub-lists indefinitely, it's usually better to stop after three levels. There are some other tricks to lists and indentation that deal with the case of paragraphs. Your paragraph must start on a line all by itself underneath the bullet point, and it must be indented by at least one space. 

```markdown

- Bulleted
- List
  - Sublist
  - Sublist

1. Numbered
2. List

```

And you will see

>- Bulleted
>- List
>  - Sublist
>  - Sublist
>
>1. Numbered
>2. List


## Paragraphs

If you forcelly insert a new line, you end up breaking the content into paragraphs. You can also accomplish this by inserting two spaces after each new line. Though the spaces are invisible, but it will break the line and start with a new paragraph.

The second _soft_ trick could be use in formatting paragraphs in lists, which will tighten the sub-paragraphs.









You can use the [editor on GitHub](https://github.com/YinpuLi/blogs/edit/gh-pages/index.md) to maintain and preview the content for your website in Markdown files.


For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/YinpuLi/blogs/settings). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://github.com/contact) and we’ll help you sort it out.
