# %TITLE%

Author: 

% your description


Configuration file (e.g. `/etc/nftables.conf`):
```
table inet filter{


	chain input {
		
		#%Comment for some rule
		%some rule

	}
	
	chain forward {
		%some rules?

	}
	
	chain output {
	
		%some rules?
		
	}

}
```
`nft`-commands that will do the same:

```
%%%
nft X
nft Y
...
%%%
```