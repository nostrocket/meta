# Event Tree

The event tree is a tree of Nostr Events with `503941a9939a4337d9aef7b92323c353441cb5ebe79f13fed77aeac615116354` as the ROOT.

- Ignition Event (ROOT)
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
                - Claim Problem (SCM)
        - Mission Statement (NSE, Replaceable)
        - Revenue Possibilities (NSE, Replaceable)
        - Git Repo for Pull Requests (SCM)
        - Investment Prohibited <bool> (SCM)
        - Investment Whitelist <pubkeys> (SCM)

## Example Message Flow
Someone wants to add a new problem to a subrocket problem tracker. They create an event for the new problem:
- tags
    - `["e", "503941a9939a4337d9aef7b92323c353441cb5ebe79f13fed77aeac615116354", <optional relay URL>, "root"]`
    - `["e", <Subrocket creation event ID>, <optional relay URL>, "subrocket"]`
    - `["e", <Problem that this problem is nested under>, <optional relay URL>, "reply"]`
    - `["e", <Another problem that this is nested under>, <optional relay URL>, "reply"]`
- content
    - `Problem statement of less than 100 characters\n\nAn elaboration on the problem statement if required. No length limit. Markdown ok.`

The client checks the Nostrocket Identity Tree to make sure that the pubkey is in the tree. The state of the Nostrocket Engine is NOT updated.

Someone else wants to claim the problem to work on it. They create an event:
- tags
    - `["e", "503941a9939a4337d9aef7b92323c353441cb5ebe79f13fed77aeac615116354", <optional relay URL>, "root"]`
    - `["e", <Subrocket creation event ID>, <optional relay URL>, "subrocket"]`
    - `["e", <Problem that they are claiming (e.g. from above)>, <optional relay URL>, "reply"]`
- content `An explanation of how they intend to solve the problem`

The client SHOULD render this as a claim if 
- no existing claim is detected in the current state
- the pubkey is in the Nostrocket Identity Tree
- that the pubkey holds no other claims on any Nostrocket or Subrocket problem

The Nostrocket Engine MUST
- Validate that the pubkey is in the identity tree
- Validate that the problem is not claimed
- Validate that the pubkey holds no other claims on any Nostrocket or Subrocket problem
- Add a new entry into the Problems `Injector` state
-  Publish the updated Subrocket state, which clients can then use to update their local state cache.

## Event Flow
When an account with `votepower > 0` witnesses a new Bitcoin Block, it SHOULD create an Event:
```
.Kind: 640001
.Content: {
    Height: <int64>[block height]
    Hash: <string>[block header]
    Time: <int64>[timestamp]
    OP_RETURN: <string>[merkle root of nostrocket state]
    }
.Tags: {
    ["e", "503941a9939a4337d9aef7b92323c353441cb5ebe79f13fed77aeac615116354", "root"],
    ["e", "event branch tip", "reply"], //repeat for every state branch that matters (e.g. not comments)
}
```

This event is a vote for every event branch that is tagged, indicating that it does not violate the protocol and was used in the creation of the current state, and delineating the end of a block.

If someone tries to create an event with an earlier timestamp, it won't work because we know that there has been a block since then and no votepower witnessed it.

If two different npubs with votepower witness conflicting events, the one with the lowest votepower should try to reproduce the state of the one with the higher votepower and go with that.

For critical states like shares, npubs with votepower should append a replaceable event to the end of the branch with *every* state change.