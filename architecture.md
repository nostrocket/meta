# Event Tree
The event tree is a tree of Nostr Events with `503941a9939a4337d9aef7b92323c353441cb5ebe79f13fed77aeac615116354` as the ROOT.

Ignition Event (ROOT)
- Add pubkey to Identity Tree (SCM, Nested)
    - Vote to remove pubkey from Identity Tree (SCM)
- New Subrocket (SCM, NSE)
    - Add pubkey to Maintaine Tree (SCM, Nested)
    - Delete pubkey from Maintainer Tree (SCM)
    - New Expense Request (SCM)
        - Vote on Expense Request (SCM)
    - Master Problem Statement (NSE, Replaceable, Maintainer Replaceable)
        - Problem Statement Long (NSE, Replaceable, Maintainer Replaceable)
        - Possible Solutions (NSE, Replaceable, Maintainer Replaceable)
        - New Problem (NSE, Maintainer Replaceable)
    - Mission Statement (NSE, Replaceable)
    - Revenue Possibilities (NSE, Replaceable)
    - Git Repo for Pull Requests (SCM)
    - Investment Prohibited <bool> (SCM)
  Investment Whitelist <pubkeys> (SCM)
  
