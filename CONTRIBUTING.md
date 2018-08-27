#Contributing

You are very welcome to publish your own nftables configuration snippets 
   but to ensure a fair bit of quality please follow the following 
   (maybe obvious) rules:
0. Look if others already published snippets for your scenario and 
   improve on them if neccessary.
1. Please use the provided [Template](TEMPLATE.md)
2. Document it in a way that other users easily understand your choices.
3. Make sure your snippets don't contain code specific to your usecase 
   only and make it as basic as possible.
   - If there are things that are different in some distributions, such 
      as network interfaces, use '$' for placeholders, eg.: $network_interface$
   - Include variants for both ipv4 and ipv6 where possible.
4. Your snippet should contain the configuration as `nft` commands _and_ 
   as lines of a config file, so readers don't have to convert them into 
   each other.
5. Add your github username to your snippet, if you think you should be 
   attributed and want to be tagged when someone has a question.

Also: this document is not set in stone and can definitely be improved, so this is one more opportunity to contribute ;-) .