### Nostrocket Ignition Event
Events MUST exist in thread replying to `503941a9939a4337d9aef7b92323c353441cb5ebe79f13fed77aeac615116354` in order to be parsed by the Nostrocket state machine.

### Event `Kind 1031`

#### New project creation Event.   

- VALIDATE: `pubkey` MUST exist in rocketree
- VALIDATE: tags MUST contain `["e", "503941a9939a4337d9aef7b92323c353441cb5ebe79f13fed77aeac615116354", <optional relay URL>, "root"]`
- VALIDATE: tags MUST contain `["e", <todo: new project thread ID>, <optional relay URL>, "reply"]`
- VALIDATE: `content` MUST be json
- VALIDATE `content.height` MUST be current Bitcoin height

### Event `Kind 31031`

#### Project metadata update and witness (parametized replaceable event)   

- VALIDATE: `e` tag MUST point to a `Kind 1031` event
- VALIDATE: `pubkey` MUST be same as `Kind 1031` in `e` tag `||` exist in `votepower` array in most recent metadata.
- VALIDATE: `content` MUST be json
- VALIDATE `content.sequence` MUST be latest `sequence + 1` 
- VALIDATE `content.height` MUST `>` `content.height` of event in `e` tag `&&` MUST `=<` current bitcoin height
- VALIDATE `["d", <parameter>]` exists in definition below
- VALIDATE `content.data` MUST follow rules defined for the parameter being set.

#### Parameters for replaceable events of `Kind 31031`

| Parameter <str> | Description | Causes State Change | Used as Witness |
| ------------- | ------------- | ------------------- | ----------- |
| captable      | the current mapping of pubkeys to shares | N | Y |
| votepower     | the current mapping of pubkeys to votepower | N | Y |
| latest_block  | the last block we saw | N | Y |
|  project_name | the name of the project | Y | Y |
| problem_statement | problem statement TL;DR; | Y | Y |
| problem_statement_long | full problem statement | Y | Y |
| possible_solutions | a discussion about possible approaches to solving | Y | Y |
| mission_statement | mission statement | Y | Y |
| revenue | a discussion about possible revenue | Y | Y |
| git_repo | the git repo that pull requests SHOULD be based on | Y | Y |
| investment_prohibited | prohibit investment? | Y | Y |
| investor_whitelist | pubkeys that are allowed to invest | Y | Y |
  
#### `content.data` for each `["d", <parameter>]`

##### captable
  This is the current mapping of pubkeys to Shares in the project. The state of Nostrocket is not updated by this kind of event, it's simply a way to say that you have witnessed this state, and people can choose to believe you or not. This is useful for a cursory look at the project from a browser, but to reach a high degree of certainty about the current state of the Cap Table you need to run the full Nostrocket State Machine. 
  
  ```
  tags: {["d", "captable"]}
  content: {"height":<int>, "data": "{"captable":"[<pubkey_hex>:<int>]", "total":<int>}
  ```
  
##### votepower
  This is the current mapping of pubkeys to Votepower in the project. Again, this is only the state of votepower according to whoever signed the event, events of this kind do not cause a state change.
##### latest_block
  This is the last block height that the pubkey witnessed. Anyone with votepower SHOULD publish this every block (web client and state machine should auto-publish this whenever it sees a block > current)
##### project_name
  This is the name of the project.
  ```
  content.data: {"project_name":<str>}
  ```
  MUST be `<` 20 characters.
##### problem_statement
  ```
  content.data: {"problem_statement":<str>}
  ```
  MUST be `<` 100 characters.
              
`Kind 1` Replies to this event are comments on the problem_statement. 
`Kind 10311` are new problems nested under this problem.
              
##### problem_statement_long
  ```
  content.data: {"problem_statement_long":<str>}
  ```
  This is an elaboration on the problem statement and should go into significant detail. 
  
##### possible_solutions
  ```
  content.data: {"possible_solutions":<str>}
  ```
  This is a discussion about the possible solutions to the problem.
  `Kind 1` Replies to this are displayed in a thread.
##### mission_statement
  ```
  content.data: {"mission_statement":<str>}
  ```
 A shared project is really just a group of people. This is the state reason for the group's existence.
##### revenue
  ```
  content.data: {"revenue":<str>}
  ```
  This is a discussion about possible ways this project could create revenue for contributors - what will people be paying for exactly?
   `Kind 1` Replies to this are displayed in a thread.
##### git_repo
  ```
  content.data: {"git_repo":<str URL>}
  ```
  This is a publicly hosted git repo which can be used to base pull requests.
   Pull requests are events of `Kind xxxx` in reply to this.
##### investment_prohibited
  ```
  content.data: {"investment_prohibited":<bool>}
  ```
  If set to True, contributors do not get the option of selling their approved expenses to investors, they can only get shares. The project will only be owned by contributors, and shares cannot be transferred to other pubkeys
##### investor_whitelist
  ```
  content.data: {"investor_whitelist": "[<str pubkey>, ...]"}
  ```
  If this is NOT empty, then only pubkeys on the list can pay for expenses.
  Can be useful, for example, if the project creator wants to retain full control.
  
### Finding the current state
There are two modes of trust in Nostrocket, each with different levels of confidence that we can attribute to the current state of Nostrocket projects.

#### Votepower based confidence
This is a level of confidence that is based on calculating votepower by replaying all events. This requires a full Nostrocket state machine which does not run in a browser. This can be thought of as a stateful Nostr indexing service with votepower based consensus. 

Users can either run their own state machine locally, or connect to a public facing one, depending on what they are doing. E.g. if any monetary value is involved they should probably run their own one locally.

This could probably run in the browser at some point using Rust to target the WASM VM, but for now I'm just going to prototype it in golang and it doesn't target WASM very well.
  
#### Witness based confidence  
This is a fallback in cases where the Nostrocket state machine is broken (will probably happen a lot initially).
We simply trust the project creator for the current state of `votepower` and derive all other state from that. 
