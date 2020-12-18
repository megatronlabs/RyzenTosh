1. Copy & paste the following code into a Terminal, elevation required.

```
ffor file in MMXCore FastCore TextModel libiomp5.dylib libtbb.dylib libtbbmalloc.dylib; do
    find /Applications/Adobe* -type f -name $file | while read -r FILE; do
        sudo -v
        echo "found $FILE"
        [[ ! -f ${FILE}.back ]] && sudo cp -f $FILE ${FILE}.back || sudo cp -f ${FILE}.back $FILE
        echo $FILE | grep libiomp5 >/dev/null
        if [[ $? == 0 ]]; then
            dir=$(dirname "$FILE")
            [[ ! -f ${HOME}/libiomp5.dylib ]] && cd $HOME && curl -sO https://github.com/megatronlabs/RyzenTosh/blob/main/AdobeFix/libiomp5.dylib
            echo -n "replacing " && sudo cp -vf ${HOME}/libiomp5.dylib $dir && echo
            rm -f ${HOME}/libiomp5.dylib
            continue
        fi
        echo $FILE | grep TextModel >/dev/null
        [[ $? == 0 ]] && echo "emptying $FILE" && sudo echo -n >$FILE && continue
        echo "patching $FILE \n"
        sudo perl -i -pe 's|\x90\x90\x90\x90\x56\xE8\x6A\x00|\x90\x90\x90\x90\x56\xE8\x3A\x00|sg' $FILE
        sudo perl -i -pe 's|\x90\x90\x90\x90\x56\xE8\x4A\x00|\x90\x90\x90\x90\x56\xE8\x1A\x00|sg' $FILE
    done
done
2. Copy & paste the following code into a Terminal window and run it.

```
[ ! -d $HOME/Library/LaunchAgents ] && mkdir $HOME/Library/LaunchAgents
AGENT=$HOME/Library/LaunchAgents/environment.plist
sysctl -n machdep.cpu.brand_string | grep FX >/dev/null 2>&1
x=$(echo $(($? != 0 ? 5 : 4)))
cat >$AGENT <<EOF
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
 <key>Label</key>
 <string>mkl-debug</string>
 <key>ProgramArguments</key>
 <array>
 <string>sh</string>
 <string>-c</string>
    <string>launchctl setenv MKL_DEBUG_CPU_TYPE $x;</string>
 </array>
 <key>RunAtLoad</key>
 <true/>
</dict>
</plist>
EOF
launchctl load ${AGENT} >/dev/null 2>&1
launchctl start ${AGENT} >/dev/null 2>&1
```

3. Reboot you Ryzentosh.

##Credit:  https://gist.github.com/naveenkrdy/26760ac5135deed6d0bb8902f6ceb6bd
