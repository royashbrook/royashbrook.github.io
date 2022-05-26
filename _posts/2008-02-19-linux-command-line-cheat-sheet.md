---
layout: post
title: linux command line cheat sheet
---

Had a burning need for some linux loving recently. I'll post about that shortly too. I found a great linux command line cheat sheet at http://www.pixelbeat.org/cmdline.html

<table class="pixelbeat">
    <tbody>
        <tr id="misc" class="pbtitle">
            <td colspan="2"><strong>Command</strong></td>
            <td><strong>Description</strong></td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">apropos whatis</td>
            <td>Show commands pertinent to string. See also <a href="http://www.pixelbeat.org/scripts/threadsafe">threadsafe</a></td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw"><a href="http://www.pixelbeat.org/lkdb/less.html">man</a> -t man | ps2pdf - &gt; man.pdf</td>
            <td>make a pdf of a manual page</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">which command</td>
            <td>Show full path name of command</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">time command</td>
            <td>See how long a command takes</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">time cat</td>
            <td>Start stopwatch. Ctrl-d to stop. See also <a href="http://www.pixelbeat.org/scripts/stopwatch/">sw</a></td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">nice <a href="http://www.pixelbeat.org/lkdb/info.html">info</a></td>
            <td>Run a low priority command (The "info" reader in this case)</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">renice 19 -p $$</td>
            <td>Make shell (script) low priority. Use for non interactive tasks</td>
        </tr>
        <tr id="dirs" class="pbtitle">
            <td colspan="3"><strong>dir navigation</strong></td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">cd -</td>
            <td>Go to previous directory</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">cd</td>
            <td>Go to $HOME directory</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">(cd dir &amp;&amp; command)</td>
            <td>Go to dir, execute command and return to current dir</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">pushd <strong>.</strong></td>
            <td>Put current dir on stack so you can <strong>popd</strong> back to it</td>
        </tr>
        <tr id="file_search" class="pbtitle">
            <td colspan="3"><strong>file searching</strong></td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw"><a href="http://www.pixelbeat.org/settings/.bashrc">alias</a> l='ls -l --color=auto'</td>
            <td>quick dir listing</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">ls -lrt</td>
            <td>List files by date. See also <a href="http://www.pixelbeat.org/scripts/newest">newest</a> and <a href="http://www.pixelbeat.org/scripts/find_mm_yyyy">find_mm_yyyy</a></td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">ls /usr/bin | pr -T9 -W$COLUMNS</td>
            <td>Print in 9 columns to width of terminal</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">find -name '*.[ch]' | xargs grep -E 'expr'</td>
            <td>Search 'expr' in this dir and below. See also <a href="http://www.pixelbeat.org/scripts/findrepo">findrepo</a></td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">find -type f -print0 | xargs -r0 grep -F 'example'</td>
            <td>Search all regular files for 'example' in this dir and below</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">find -maxdepth 1 -type f | xargs grep -F 'example'</td>
            <td>Search all regular files for 'example' in this dir</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">find -maxdepth 1 -type d | while <a href="http://www.pixelbeat.org/programming/readline/">read</a> dir; do echo $dir; echo cmd2; done</td>
            <td>Process each item with multiple commands (in while loop)</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">find -type f ! -perm -444</td>
            <td>Find files not readable by all (useful for web site)</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">find -type d ! -perm -111</td>
            <td>Find dirs not accessible by all (useful for web site)</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">locate -r 'file[^/]*\.txt'</td>
            <td>Search cached index for names. This re is like glob *file*.txt</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">look reference</td>
            <td>Quickly search (sorted) dictionary for prefix</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">grep <a href="http://www.pixelbeat.org/settings/.bashrc">--color</a> reference /usr/share/dict/words</td>
            <td>Highlight occurances of regular expression in dictionary</td>
        </tr>
        <tr id="archives" class="pbtitle">
            <td colspan="3"><strong>archives and compression</strong></td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">gpg -c file</td>
            <td>Encrypt file</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">gpg file.gpg</td>
            <td>Decrypt file</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">tar -c dir/ | bzip2 &gt; dir.tar.bz2</td>
            <td>Make compressed archive of dir/</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">bzip2 -dc dir.tar.bz2 | tar -x</td>
            <td>Extract archive (use gzip instead of bzip2 for tar.gz files)</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">tar -c dir/ | gzip | gpg -c | ssh user@remote 'dd of=dir.tar.gz.gpg'</td>
            <td>Make encrypted archive of dir/ on remote machine</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">find dir/ -name '*.txt' | tar -c --files-from=- | bzip2 &gt; dir_txt.tar.bz2</td>
            <td>Make archive of subset of dir/ and below</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">find dir/ -name '*.txt' | xargs cp -a --target-directory=dir_txt/ --parents</td>
            <td>Make copy of subset of dir/ and below</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">( tar -c /dir/to/copy ) | ( cd /where/to/ &amp;&amp; tar -x -p )</td>
            <td>Copy (with permissions) copy/ dir to /where/to/ dir</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">( cd /dir/to/copy &amp;&amp; tar -c <strong>.</strong> ) | ( cd /where/to/ &amp;&amp; tar -x -p )</td>
            <td>Copy (with permissions) contents of copy/ dir to /where/to/</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">( tar -c /dir/to/copy ) | ssh -C user@remote 'cd /where/to/ &amp;&amp; tar -x -p' </td>
            <td>Copy (with permissions) copy/ dir to remote:/where/to/ dir</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">dd bs=1M if=/dev/sda | gzip | ssh user@remote 'dd of=sda.gz'</td>
            <td>Backup harddisk to remote machine</td>
        </tr>
        <tr id="rsync" class="pbtitle">
            <td colspan="3"><strong>rsync</strong> (Use the --dry-run option for testing)</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">rsync -P rsync://rsync.server.com/path/to/file file</td>
            <td>Only get diffs. Do multiple times for troublesome downloads</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">rsync --bwlimit=1000 fromfile tofile</td>
            <td>Locally copy with rate limit. It's like nice for I/O</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">rsync -az -e ssh --delete ~/public_html/ remote.com:'~/public_html'</td>
            <td>Mirror web site (using compression and encryption)</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">rsync -auz -e ssh remote:/dir/ <strong>.</strong> &amp;&amp; rsync -auz -e ssh <strong>.</strong> remote:/dir/</td>
            <td>Synchronize current directory with remote one</td>
        </tr>
        <tr id="ssh" class="pbtitle">
            <td colspan="3"><strong>ssh</strong> (Secure SHell)</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">ssh $USER@$HOST command</td>
            <td>Run command on $HOST as $USER (default command=shell)</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">ssh -f -Y $USER@$HOSTNAME xeyes</td>
            <td>Run GUI command on $HOSTNAME as $USER</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">scp -p -r $USER@$HOST: file dir/</td>
            <td>Copy with permissions to $USER's home directory on $HOST</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">ssh -g -L 8080:localhost:80 root@$HOST</td>
            <td>Forward connections to $HOSTNAME:8080 out to $HOST:80</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">ssh -R 1434:imap:143 root@$HOST</td>
            <td>Forward connections from $HOST:1434 in to imap:143</td>
        </tr>
        <tr id="wget" class="pbtitle">
            <td colspan="3"><strong>wget</strong> (multi purpose download tool)</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">(cd cmdline &amp;&amp; wget -nd -pHEKk http://www.pixelbeat.org/cmdline.html)</td>
            <td>Store local browsable version of a page to the current dir</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">wget -c http://www.example.com/large.file</td>
            <td>Continue downloading a partially downloaded file</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">wget -r -nd -np -l1 -A '*.jpg' http://www.example.com/dir/</td>
            <td>Download a set of files to the current directory</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">wget ftp://remote/file[1-9].iso/</td>
            <td>FTP supports globbing directly</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">wget -q -O- http://www.pixelbeat.org/timeline.html | grep 'a href' | head</td>
            <td>Process output directly</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">echo 'wget url' | at 01:00</td>
            <td>Download url at 1AM to current dir</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">wget --limit-rate=20k url</td>
            <td>Do a low priority download (limit to 20<a href="http://www.pixelbeat.org/speeds.html">KB/s</a> in this case)</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">wget -nv --spider --force-html -i bookmarks.html</td>
            <td>Check links in a file</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">wget --mirror http://www.example.com/</td>
            <td>Efficiently update a local copy of a site (handy from cron)</td>
        </tr>
        <tr id="net" class="pbtitle">
            <td colspan="3"><strong>networking</strong> (Note ifconfig, route, mii-tool, nslookup commands are obsolete)</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw"><acronym title="usually in /sbin/">ethtool</acronym> eth0</td>
            <td>Show status of ethernet interface eth0</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">ethtool --change eth0 autoneg off speed 100 duplex full</td>
            <td>Manually set ethernet interface speed</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw"><acronym title="usually in /sbin/">iwconfig</acronym> eth1</td>
            <td>Show status of wireless interface eth1</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">iwconfig eth1 rate 1Mb/s fixed</td>
            <td>Manually set wireless interface speed</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw"><acronym title="usually in /sbin/">iwlist</acronym> scan</td>
            <td>List wireless networks in range</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw"><acronym title="usually in /sbin/">ip</acronym> link show</td>
            <td>List network interfaces</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">ip link set dev eth0 name wan</td>
            <td>Rename interface eth0 to wan</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">ip link set dev eth0 up</td>
            <td>Bring interface eth0 up (or down)</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">ip addr show</td>
            <td>List addresses for interfaces</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">ip addr add 1.2.3.4/24 brd + dev eth0</td>
            <td>Add (or del) ip and mask (255.255.255.0)</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">ip route show</td>
            <td>List routing table</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">ip route add default via 1.2.3.254</td>
            <td>Set default gateway to 1.2.3.254</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw"><acronym title="usually in /sbin/">tc</acronym> qdisc add dev lo root handle 1:0 netem delay 20msec</td>
            <td>Add 20ms latency to loopback device (for testing)</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">tc qdisc del dev lo root</td>
            <td>Remove latency added above</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">host pixelbeat.org</td>
            <td>Lookup DNS ip address for name or vice versa</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">hostname -i</td>
            <td>Lookup local ip address (equivalent to host `hostname`)</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">whois pixelbeat.org</td>
            <td>Lookup whois info for hostname or ip address</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">netstat -tupl</td>
            <td>List internet services on a system</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">netstat -tup</td>
            <td>List active connections to/from system</td>
        </tr>
        <tr id="windows" class="pbtitle">
            <td colspan="3"><strong>windows networking</strong> (Note samba is the package that provides all this windows specific networking support)</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">smbtree</td>
            <td>Find windows machines. See also findsmb</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">nmblookup -A 1.2.3.4</td>
            <td>Find the windows (netbios) name associated with ip address</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">smbclient -L windows_box</td>
            <td>List shares on windows machine or samba server</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">mount -t smbfs -o fmask=666,guest //windows_box/share /mnt/share</td>
            <td>Mount a windows share</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">echo 'message' | smbclient -M windows_box</td>
            <td>Send popup to windows machine (off by default in XP sp2)</td>
        </tr>
        <tr id="text" class="pbtitle">
            <td colspan="3"><strong>text manipulation</strong> (Note sed uses stdin and stdout, so if you want to edit files, append <em>&lt;oldfile &gt;newfile</em>)</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">sed 's/string1/string2/g'</td>
            <td>Replace string1 with string2</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">sed 's/\(.*\)1/\12/g'</td>
            <td>Modify anystring1 to anystring2</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">sed '/ *#/d; /^ *$/d'</td>
            <td>Remove comments and blank lines</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">sed ':a; /\\$/N; s/\\\n//; ta'</td>
            <td>Concatenate lines with trailing \</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">sed 's/[ \t]*$//'</td>
            <td>Remove trailing spaces from lines</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">sed 's/\([\\`\\"$\\\\]\)/\\\1/g'</td>
            <td>Escape shell metacharacters active within double quotes</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">seq 10 | sed "s/^/      /; s/ *\(.\{7,\}\)/\1/"</td>
            <td>Right align numbers</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">sed -n '1000p;<acronym title="quit ASAP">1000q</acronym>'</td>
            <td>Print 1000th line</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">sed -n '10,20p;<acronym title="quit ASAP">20q</acronym>'</td>
            <td>Print lines 10 to 20</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">sed -n 's/.*&lt;title&gt;\(.*\)&lt;\/title&gt;.*/\1/ip;<acronym title="quit after match">T;q</acronym>'</td>
            <td>Extract title from HTML web page</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">sort -t. -k1,1n -k2,2n -k3,3n -k4,4n</td>
            <td>Sort IPV4 ip addresses</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">echo 'Test' | tr '[:lower:]' '[:upper:]'</td>
            <td>Case conversion</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">tr -dc '[:print:]' &lt; /dev/urandom</td>
            <td>Filter non printable characters</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">history | wc -l</td>
            <td>Count lines</td>
        </tr>
        <tr id="sets" class="pbtitle">
            <td colspan="3"><strong>set operations</strong> (Note you can <a href="http://www.pixelbeat.org/docs/env.html">export LANG=C</a> for speed. Also these assume no duplicate lines within a file)</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">sort file1 file2 | uniq</td>
            <td><acronym title="Items in either file1 or file2">Union</acronym> of unsorted files</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">sort file1 file2 | uniq -d</td>
            <td><acronym title="Items both in file1 and file2">Intersection</acronym> of unsorted files</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">sort file1 file1 file2 | uniq -u</td>
            <td><acronym title="Items in file2 not in file1">Difference</acronym> of unsorted files</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">sort file1 file2 | uniq -u</td>
            <td><acronym title="Items in only one file">Symmetric Difference</acronym> of unsorted files</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">join -a1 -a2 file1 file2</td>
            <td>Union of sorted files</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">join file1 file2</td>
            <td>Intersection of sorted files</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">join -v2 file1 file2</td>
            <td>Difference of sorted files</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">join -v1 -v2 file1 file2</td>
            <td>Symmetric Difference of sorted files</td>
        </tr>
        <tr id="math" class="pbtitle">
            <td colspan="3"><strong>math</strong></td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">echo '(1 + sqrt(5))/2' | bc -l</td>
            <td>Quick math (Calculate φ). See also <a href="http://www.pixelbeat.org/scripts/bc">bc</a></td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">echo 'pad=20; min=64; (100*10^6)/((pad+min)*8)' | bc</td>
            <td>More complex (int) e.g. This shows max FastE packet rate</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">echo 'pad=20; min=64; print (100E6)/((pad+min)*8)' | python</td>
            <td>Python handles scientific notation</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">echo 'pad=20; plot [64:1518] (100*10**6)/((pad+x)*8)' | gnuplot -persist</td>
            <td>Plot FastE packet rate vs packet size</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">echo 'obase=16; ibase=10; 64206' | bc</td>
            <td>Base conversion (decimal to hexadecimal)</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">echo $((0x2dec))</td>
            <td>Base conversion (hex to dec) ((shell arithmetic expansion))</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">units -t '100m/9.74s' 'miles/hour'</td>
            <td>Unit conversion (metric to imperial)</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">units -t '500GB' 'GiB'</td>
            <td>Unit conversion (<acronym title="powers of 10">SI</acronym> to <acronym title="powers of 2">IEC</acronym> prefixes)</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">units -t '1 googol'</td>
            <td>Definition lookup</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">seq 100 | (tr '\n' +; echo 0) | bc</td>
            <td>Add a column of numbers. See also <a href="http://www.pixelbeat.org/scripts/add">add</a> and <a href="http://www.pixelbeat.org/scripts/funcpy">funcpy</a></td>
        </tr>
        <tr id="dates" class="pbtitle">
            <td colspan="3"><strong>calendar</strong></td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">cal -3</td>
            <td>Display a calendar</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">cal 9 1752</td>
            <td>Display a calendar for a particular month year</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">date -d fri</td>
            <td>What date is it this friday. See also <a href="http://www.pixelbeat.org/scripts/day">day</a></td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">date --date='25 Dec' +%A</td>
            <td>What day does xmas fall on, this year</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">date --date '1970-01-01 UTC 2147483647 seconds'</td>
            <td>Convert number of seconds since the epoch to a date</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">TZ=':America/Los_Angeles' date</td>
            <td>What time is it on West coast of US (use tzselect to find TZ)</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">echo "mail -s 'get the train' P@draigBrady.com &lt; /dev/null" | at 17:45</td>
            <td>Email reminder</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">echo "DISPLAY=$DISPLAY xmessage cooker" | at "NOW + 30 minutes"</td>
            <td>Popup reminder</td>
        </tr>
        <tr id="locale" class="pbtitle">
            <td colspan="3"><strong>locales</strong></td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">printf "%'d\n" 1234</td>
            <td>Print number with thousands grouping appropriate to locale</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">BLOCK_SIZE=\'1 ls -l</td>
            <td>get ls to do thousands grouping appropriate to locale</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">echo "I live in `locale territory`"</td>
            <td>Extract info from locale database</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">LANG=en_IE.utf8 locale int_prefix</td>
            <td>Lookup locale info for specific country. See also <a href="http://www.pixelbeat.org/scripts/ccodes">ccodes</a></td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">locale | cut -d= -f1 | xargs locale -kc | less</td>
            <td>List fields available in locale database</td>
        </tr>
        <tr id="recode" class="pbtitle">
            <td colspan="3"><strong>recode</strong> (Obsoletes iconv, dos2unix, unix2dos)</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">recode -l | less</td>
            <td>Show available conversions (aliases on each line)</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">recode windows-1252.. file_to_change.txt</td>
            <td>Windows "ansi" to local charset (auto does CRLF conversion)</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">recode utf-8/CRLF.. file_to_change.txt</td>
            <td>Windows utf8 to local charset</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">recode iso-8859-15..utf8 file_to_change.txt</td>
            <td>Latin9 (western europe) to utf8</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">recode ../b64 &lt; file.txt &gt; file.b64</td>
            <td>Base64 encode</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">recode /qp.. &lt; file.txt &gt; file.qp</td>
            <td>Quoted printable decode</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">recode ..HTML &lt; file.txt &gt; file.html</td>
            <td>Text to HTML</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">recode -lf windows-1252 | grep euro</td>
            <td>Lookup <a href="http://www.pixelbeat.org/docs/utf8.html">table of characters</a></td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">echo -n 0x80 | recode latin-9/x1..dump</td>
            <td>Show what a code represents in latin-9 charmap</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">echo -n 0x20AC | recode ucs-2/x2..latin-9/x</td>
            <td>Show latin-9 encoding</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">echo -n 0x20AC | recode ucs-2/x2..utf-8/x</td>
            <td>Show utf-8 encoding</td>
        </tr>
        <tr id="CDs" class="pbtitle">
            <td colspan="3"><strong><acronym title="Compact Disks">CDs</acronym></strong></td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">gzip &lt; /dev/cdrom &gt; cdrom.iso.gz</td>
            <td>Save copy of data cdrom</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">mkisofs -V LABEL -r dir | gzip &gt; cdrom.iso.gz</td>
            <td>Create cdrom image from contents of dir</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">mount -o loop cdrom.iso /mnt/dir</td>
            <td>Mount the cdrom image at /mnt/dir (read only)</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">cdrecord -v dev=/dev/cdrom blank=fast</td>
            <td>Clear a CDRW</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">gzip -dc cdrom.iso.gz | cdrecord -v dev=/dev/cdrom -</td>
            <td>Burn cdrom image (use dev=ATAPI -scanbus to confirm dev)</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">cdparanoia -B</td>
            <td>Rip audio tracks from CD to wav files in current dir</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">cdrecord -v dev=/dev/cdrom -audio *.wav</td>
            <td>Make audio CD from all wavs in current dir (see also cdrdao)</td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">oggenc --tracknum='track' track.cdda.wav -o 'track.ogg'</td>
            <td>Make ogg file from wav file</td>
        </tr>
        <tr id="disk_space" class="pbtitle">
            <td colspan="3"><strong>disk space</strong> (See also <a href="http://www.pixelbeat.org/fslint/">FSlint</a>)</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">ls -lSr</td>
            <td>Show files by size, biggest last</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">du -s * | sort -k1,1rn | head</td>
            <td>Show top disk users in current dir. See also <a href="http://www.pixelbeat.org/scripts/dutop">dutop</a></td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">df -h</td>
            <td>Show free space on mounted filesystems</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">df -i</td>
            <td>Show free inodes on mounted filesystems</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw"><acronym title="usually in /sbin/">fdisk</acronym> -l</td>
            <td>Show disks partitions sizes and types (run as root)</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw"><a href="http://www.pixelbeat.org/docs/packaging.html">rpm</a> -q -a --qf '%10{SIZE}\t%{NAME}\n' | sort -k1,1n</td>
            <td>List all <a href="http://www.pixelbeat.org/docs/packaging.html">packages</a> by installed size (Bytes) on rpm distros</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw"><a href="http://www.pixelbeat.org/docs/packaging.html">dpkg</a>-query -W -f='${Installed-Size;10}\t${Package}\n' | sort -k1,1n</td>
            <td>List all <a href="http://www.pixelbeat.org/docs/packaging.html">packages</a> by installed size (KBytes) on deb distros</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">dd bs=1 seek=2TB if=/dev/null of=ext3.test</td>
            <td>Create a large test file (taking no space). See also <a href="http://www.pixelbeat.org/scripts/truncate">truncate</a></td>
        </tr>
        <tr id="monitor" class="pbtitle">
            <td colspan="3"><strong>monitoring/debugging</strong></td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">tail -f /var/log/messages</td>
            <td><a href="http://www.pixelbeat.org/docs/web/access_log/monitoring.html">Monitor messages</a> in a log file</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">strace -c ls &gt;/dev/null</td>
            <td>Summarise/profile system calls made by command</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">strace -f -e open ls &gt;/dev/null</td>
            <td>List system calls made by command</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">ltrace -f -e getenv ls &gt;/dev/null</td>
            <td>List library calls made by command</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw"><acronym title="usually in /usr/sbin/">lsof</acronym> -p <acronym title="process id of current shell">$$</acronym></td>
            <td>List paths that process id has open</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">lsof ~</td>
            <td>List processes that have specified path open</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">tcpdump not port 22</td>
            <td>Show network traffic except ssh. See also <a href="http://www.pixelbeat.org/scripts/tcpdump_not_me">tcpdump_not_me</a></td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">ps -e -o pid,args --forest</td>
            <td>List processes in a hierarchy</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">ps -e -o pcpu,cpu,nice,state,cputime,args --sort pcpu | sed '/^ 0.0 /d'</td>
            <td>List processes by % cpu usage</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">ps -e -orss=,args= | sort -b -k1,1n | pr -TW$COLUMNS</td>
            <td>List processes by mem usage. See also <a href="http://www.pixelbeat.org/scripts/ps_mem.py">ps_mem.py</a></td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">ps -C firefox-bin -L -o pid,tid,pcpu,state</td>
            <td>List all threads for a particular process</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">ps -p 1,2</td>
            <td>List info for particular process IDs</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">last reboot</td>
            <td>Show system reboot history</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">free -m</td>
            <td>Show amount of (remaining) RAM (-m displays in MB)</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">watch -n1 'cat /proc/interrupts'</td>
            <td>Watch changeable data continuously</td>
        </tr>
        <tr id="sysinfo" class="pbtitle">
            <td colspan="3"><strong>system information</strong> (see also <a href="http://www.pixelbeat.org/scripts/sysinfo">sysinfo</a>) ('#' means root access is required)</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">uname -a</td>
            <td>Show kernel version and system architecture</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">head -n1 /etc/issue</td>
            <td>Show name and version of distribution</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">cat /proc/partitions</td>
            <td>Show all partitions registered on the system</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">grep MemTotal /proc/meminfo</td>
            <td>Show RAM total seen by the system</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">grep "model name" /proc/cpuinfo</td>
            <td>Show CPU(s) info</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw"><acronym title="usually in /sbin/">lspci</acronym> -tv</td>
            <td>Show PCI info</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw"><acronym title="usually in /sbin/">lsusb</acronym> -tv</td>
            <td>Show USB info</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">mount | column -t</td>
            <td>List mounted filesystems on the system (and align output)</td>
        </tr>
        <tr>
            <td>#</td>
            <td class="nw">dmidecode -q | less</td>
            <td>Display SMBIOS/DMI information</td>
        </tr>
        <tr>
            <td>#</td>
            <td class="nw">smartctl -A /dev/sda | grep Power_On_Hours</td>
            <td>How long has this disk (system) been powered on in total</td>
        </tr>
        <tr>
            <td>#</td>
            <td class="nw">hdparm -i /dev/sda</td>
            <td>Show info about disk sda</td>
        </tr>
        <tr>
            <td>#</td>
            <td class="nw">hdparm -tT /dev/sda</td>
            <td>Do a read speed test on disk sda</td>
        </tr>
        <tr>
            <td>#</td>
            <td class="nw">badblocks -s /dev/sda</td>
            <td>Test for unreadable blocks on disk sda</td>
        </tr>
        <tr id="interactive" class="pbtitle">
            <td colspan="3"><strong>interactive</strong> (see also <a href="http://www.pixelbeat.org/lkdb/">linux keyboard shortcuts)</a></td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw"><a href="http://www.pixelbeat.org/lkdb/readline.html">readline</a></td>
            <td>Line editor used by bash, python, bc, gnuplot, ...</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw"><a href="http://www.pixelbeat.org/lkdb/screen.html">screen</a></td>
            <td>Virtual terminals with detach capability, ...</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw"><a href="http://www.pixelbeat.org/lkdb/mc.html">mc</a></td>
            <td>Powerful file manager that can browse rpm, tar, ftp, ssh, ...</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw"><a href="http://www.pixelbeat.org/docs/web/access_log/analyzing.html">gnuplot</a></td>
            <td>Interactive/scriptable graphing</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">links</td>
            <td>Web browser</td>
        </tr>
        <tr id="misc" class="pbtitle">
            <td colspan="3"><strong>miscellaneous</strong></td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw"><a href="http://www.pixelbeat.org/settings/.bashrc">alias</a> hd='od -Ax -tx1z -v'</td>
            <td>Handy hexdump. (usage e.g.: • hd /proc/self/cmdline | less)</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw"><a href="http://www.pixelbeat.org/settings/.bashrc">alias</a> realpath='readlink -f'</td>
            <td>Canonicalize path. (usage e.g.: • realpath ~/../$USER)</td>
        </tr>
        <tr>
            <td>•</td>
            <td class="nw">set | grep $USER</td>
            <td>Search current <a href="http://www.pixelbeat.org/docs/env.html">environment</a></td>
        </tr>
        <tr>
            <td> </td>
            <td class="nw">touch -c -t 0304050607 file</td>
            <td>Set file timestamp (YYMMDDhhmm)</td>
        </tr>
    </tbody>
</table>
