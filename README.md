# Overview

Various "The Towers" maps made by [The Towers Champagne](https://discord.gg/p46tXVVETr) based on [PGM](https://github.com/PGMDev/PGM).

## Includes

This repository uses custom include files to improve gameplay behavior. The following includes are required in maps where their mechanics are relevant.

---

### `gapple-kill-reward.xml`

```xml
<map>
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

### `towers-bridge.xml
```xml
<map>
<include id="gapple-kill-reward"/>
<include id="void-death"/>
<teams>
    <team id="blue-team" color="dark blue" max="2">Blue</team>
    <team id="red-team" color="dark red" max="2">Red</team>
</teams>
<kits>
    <kit id="spawn-kit">
        <max-health>20</max-health>
        <item slot="0" unbreakable="true" material="stone sword"/>
        <item slot="1" unbreakable="true" material="bow"/>
        <item slot="2" amount="1" material="golden apple"/>
        <item slot="3" unbreakable="true" enchantment="efficiency:2" material="iron pickaxe"/>
        <item slot="4" amount="32" material="brick"/>
        <item slot="8" amount="5" material="arrow"/>
        <helmet team-color="true" unbreakable="true" material="leather helmet"/>
        <chestplate team-color="true" unbreakable="true" material="leather chestplate"/>
        <leggings team-color="true" unbreakable="true" enchantment="protection projectile:2" material="chainmail leggings"/>
        <boots team-color="true" unbreakable="true" material="iron boots"/>
        <effect duration="1s" amplifier="5">resistance</effect>
        <effect duration="1s" amplifier="5">regeneration</effect>
        <clear/>
    </kit>
    <kit id="kill">
        <max-health>1</max-health>
        <effect duration="5">instant_damage</effect>
    </kit>
    <kit id="goal-kit">
        <action>
            <set var="player_score" value="player_score+1"/>
        </action>
        <action>
            <message actionbar="`7Points: `a{amount}">
                <replacements>
                    <decimal id="amount" value="player_score"/>
                </replacements>
            </message>
        </action>
    </kit>
</kits>
<variables>
    <variable id="player_score" scope="player" default="0"/>
</variables>
<stats>
    <stat name="Points" value="player_score"/>
</stats>
<filters>
    <team id="only-blue">blue-team</team>
    <team id="only-red">red-team</team>
    <material id="brick">brick</material>
    <players id="at-least-8" min="8" participants="true">
        <participating/>
    </players>
    <all id="red-scored">
        <team>red-team</team>
        <region id="blue-scorebox"/>
    </all>
    <all id="blue-scored">
        <team>blue-team</team>
        <region id="red-scorebox"/>
        <participating/>
    </all>
</filters>
<toolrepair>
    <tool>stone sword</tool>
    <tool>iron pickaxe</tool>
    <tool>bow</tool>
</toolrepair>
<itemremove>
    <item>leather helmet</item>
    <item>leather chestplate</item>
    <item>chainmail leggings</item>
    <item>iron boots</item>
</itemremove>
<itemkeep>
    <item>golden apple</item>
    <item>brick</item>
    <item>arrow</item>
</itemkeep>
<kill-rewards>
    <kill-reward>
        <kit deduct-items="true">
            <item amount="32" material="brick"/>
            <item amount="5" material="arrow"/>
        </kit>
    </kill-reward>
</kill-rewards>
<hunger>
    <depletion>off</depletion>
</hunger>
<renewables>
    <renewable region = "everywhere" interval="10s" particles="false" avoid-players="0">
        <renew-filter>
            <any>
                <material>air</material>
            </any>
        </renew-filter>
        <replace-filter>
            <any>
                <material>brick</material>
            </any>
        </replace-filter>
    </renewable>
</renewables>
<respawns>
  <respawn delay="3s" filter="at-least-8" auto="true" blackout="true"/>
</respawns>
<respawn delay="2s" auto="true" blackout="true"/>
<disabledamage>
    <damage>fall</damage>
</disabledamage>
<modifybowprojectile pickup-filter="never"/>
<score>
    <limit>10</limit>
    <box value="1" filter="only-red" region="blue-scorebox"/>
    <box value="1" filter="only-blue" region="red-scorebox"/>
</score>
<time overtime="2m" max-overtime="3m">10m</time>
<block-drops>
    <rule wrong-tool="false">
        <filter>
            <any>
                <material>BRICK</material>
            </any>
        </filter>
    </rule>
</block-drops>
</map>
```