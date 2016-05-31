##TTTAttributedLabel

When the time comes to add clickable links to your app, you will quickly find that both UILabel and UITextField APIs just don't quite get the job done. UILabels offer nice formatting options and can display links added to attributed text, but do not support clickable links. UITextViews do support clickable links as well as auto link detection, but lack the formatting options that make UILabels so useful and the whole text view becomes clickable, not just the linked text. Thankfully, there is [TTTAttributedLabel](https://github.com/TTTAttributedLabel/TTTAttributedLabel), a CocoaPod silver bullet that adds awesome link handling to the trusty and reliable label.  