#### Nostrocket Ignition Event
Events MUST exist in thread replying to `503941a9939a4337d9aef7b92323c353441cb5ebe79f13fed77aeac615116354` in order to be parsed by the Nostrocket state machine.

#### Event `Kind 1031`: new project creation Event   
- VALIDATE: `pubkey` MUST exist in rocketree
- VALIDATE: tags MUST contain `["e", "503941a9939a4337d9aef7b92323c353441cb5ebe79f13fed77aeac615116354", <optional relay URL>, "root"]`
- VALIDATE: tags MUST contain `["e", <todo>, <optional relay URL>, "reply"]`
- VALIDATE: `content` MUST be json
- VALIDATE `content.height` MUST be current Bitcoin height

#### Event `Kind 31031`: project metadata update and witness (parametized replaceable event)   
- VALIDATE: `e` tag MUST point to a `Kind 1031` event
- VALIDATE: `pubkey` MUST be same as `Kind 1031` in `e` tag `||` exist in `votepower` array in most recent metadata.
- VALIDATE: `content` MUST be json
- VALIDATE `content.sequence` MUST be latest `sequence + 1` 
- VALIDATE `content.height` MUST `>` `content.height` of event in `e` tag `&&` MUST `=<` current bitcoin height
- VALIDATE `["d", <parameter>]` exists in definition below
- VALIDATE `content.data` MUST follow rules defined for the parameter being set.

##### `content.data` for each `["d", <parameter>]`
| Parameter <str> | Description | Causes State Change | Used as Witness |
| ------------- | ------------- | ------------------- | ----------- |
| captable      | the current mapping of pubkeys to shares | N | Y |
| votepower     | the current mapping of pubkeys to votepower | N | Y |
| latest_block  | the last block we saw | N | Y |
|  project_name | the name of the project | Y | Y |
| problem_statement | problem statement TL;DR; | Y | Y |
| problem_statement_long | full problem statement | Y | Y |
  
- current cap table
- last block height they saw (their client should auto-publish this whenever they see a block > current)
- Project Name
- Problem Statement TLDR
- Problem Statement Expanded
- Possible Solutions
- Project Description
- public Git repo for pull requests to be based on
- Possible revenue model ideas (what will people be paying for)
- Investment Prohibited? BOOL (can investors buy expense requests? If true, project will only be owned by contributors, and shares cannot be transferred to other pubkeys)
- Allowed Investor pubkeys (unrestricted if empty, project creator can add their own pubkey if they want to be the only one who can invest)
