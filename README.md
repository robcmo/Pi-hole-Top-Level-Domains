# Pi-hole Top-Level Domains

This project uses the DNS queries on a Pi-hole to get a list of top-level domains contacted from a network. Sharing for anyone that may find this useful... the results may surprise you. This day project is the outcome of noticing my digital scale talking back to China.

## Pi-hole database query

By using FTL, we're able to get a list of all domains from 365 days ago up through one minute ago.

```bash
# Get list of domains from pihole-FTL database.
sqlite3 "/etc/pihole/pihole-FTL.db" "SELECT domain FROM queries GROUP BY domain ORDER BY domain" > domains.txt
```

## Pi-hole log query

By using logs, we're able to get a list of all domains from a specific day in past week.

```bash
# Get list of domains from today's pihole.log; yesterday's is pihole.log.1
cat /var/log/pihole.log | cut -d ' ' -f 6 | sort | uniq > domains.txt
```

```bash
# Prior logs are compressed so use zcat or zgrep to read or search.
zcat /var/log/pihole.log.2.gz | cut -d ' ' -f 6 | sort | uniq > domains.txt
```

## TLDs

This could have been combined to one step without using a file, but this allows for easy queries from the results. Look through your TLD list and drilldown with grep.

```bash
# List all TLDs.
cat domains.txt | awk -F'.' '{print $(NF)}' | sort | uniq

# List all fully-qualified domains registered to China.
grep 'cn$' domains.txt

# List all fully-qualified domains with two-letter TLDs.
grep '\.[a-z][a-z]$' domains.txt
```

## Block TLD

Example: Block all Chinese registered domains.

### Web Option

On Pi-hole web-interface, click Blacklist, type `cn` for Domain and `China` for Comment. Click `Add domain as wildcard` and `Add to Blacklist`.

### Shell Option

```bash
pihole --regex '(\.|^)cn$'
```

## References

* [Pi-hole documentation](https://docs.pi-hole.net/)

* [Pi-hole documentation: Query database](https://docs.pi-hole.net/database/ftl/)

* [Wikipedia: List of Internet top-level domains](https://en.wikipedia.org/wiki/List_of_Internet_top-level_domains)

* [TheEdgeOfCosmos' Pi-hole statistics scripts](https://www.reddit.com/r/pihole/comments/gk1uiu/some_scripts_for_pi_hole_statistics/)
