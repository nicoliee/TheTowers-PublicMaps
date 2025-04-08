# Overview

Various "The Towers" maps made by [The Towers Champagne](https://discord.gg/p46tXVVETr) based on [PGM](https://github.com/PGMDev/PGM).

## Includes

This repository uses custom include files to improve gameplay behavior. The following includes are required in maps where their mechanics are relevant.

---

### `gapple-kill-reward.xml`

```xml
<map> <!-- minimum proto 1.4.0 -->
    <!-- Golden apple kill reward include file -->
    <!-- Author: Pablete1234 -->
    <!-- Version 2024.12.12-1 -->
    <!--
        This include file prevents golden apple consumption from being interrupted
        after killing another play and receiving a golden apple as a kill reward.
    -->

    <variables>
        <variable id="missing_gaps" scope="player"/>
    </variables>

    <filters>
        <holding id="holding-gap" ignore-metadata="true">
            <item>golden apple</item>
        </holding>
        <pulse id="pulse-give-gap" duration="0.05s" period="0.1s" filter="all(not(holding-gap),missing_gaps=1..)"/>
    </filters>

    <actions>
        <action id="give-gap" scope="player">
            <kit deduct-items="false">
                <item material="golden apple"/>
            </kit>
            <set var="missing_gaps" value="missing_gaps-1"/>
        </action>
        <trigger scope="player" filter="pulse-give-gap" action="give-gap"/>
    </actions>

    <kill-rewards>
        <kill-reward>
            <action>
                <set var="missing_gaps" value="missing_gaps+1"/>
                <action filter="not(holding-gap)">
                    <action id="give-gap"/>
                </action>
            </action>
        </kill-reward>
    </kill-rewards>
</map>
```

### `void-death.xml`

```xml
<map> <!-- minimun proto 1.4.0 -->
    <!-- version 2024.12.05-1 -->

    <constants fallback="true"> <!-- Default values. Each of them be modified by authors on their maps if needed. -->
        <constant id="void-plane">-5</constant> <!-- Y level to apply include effects -->
        <constant id="affect-obs">never</constant> <!-- Will observers also get teleported to their spawn on contact with the region? -->
        <constant id="health">1</constant> <!-- What health value should be set in the void region? -->
        <constant id="override-health">true</constant> <!-- Should health be overridden at all? -->
    </constants>

    <kits>
        <kit id="void-death" force="true">
            <if constant="override-health" constant-value="true">
                <health>${health}</health>
            </if>
            <clear effects="true" items="false" armor="false"/>
        </kit>
    </kits>

    <portals>
        <portal y="@-110" sound="false" observers="${affect-obs}">
            <region>
                <below y="${void-plane}"/>
            </region>
        </portal>
    </portals>

    <regions>
        <apply kit="void-death">
            <region>
                <below y="${void-plane}"/>
            </region>
        </apply>
    </regions>
</map>
```