
# INSU 6 - Bugs und Features

Hier kannst du Offiziele Bilder, Codes, API's und Bug sowie Feature Request anfragen. Mit deiner Hilfe können wir INSU immer Besser und Besser machen.


## Inhaltsverzeichniss

 - [FAQ](https://github.com/Team-Flames/INSU-LISTE/blob/main/README.md#faq)
 - [Neuerungen](https://github.com/Team-Flames/INSU-LISTE/blob/main/README.md#Neuerungen)
 - [Einen Bug Melden](https://github.com/Team-Flames/INSU-LISTE/blob/main/README.md#faq)
 - [Ein Feature Vorschlagen](https://github.com/Team-Flames/INSU-LISTE/blob/main/README.md#faq)
 - [Verwendete API's](https://github.com/Team-Flames/INSU-LISTE/blob/main/README.md#faq)
 - [Code Beispiele](https://github.com/Team-Flames/INSU-LISTE/blob/main/README.md#faq)



## FAQ

#### Wann genau Startet INSU?

Voraussichtlich am 31.03.2023

#### Wie Viele Teilnehmer machen dort mit?

Es machen ca. 50 Teilnehmer mit.

#### Wie viele Events wird es ca. geben

Es gibt mind. 5 Events.

#### Wird wieder TP-UP dabei sein?

Ja.


## Neuerungen

Alle Neuerungen im Vergleich von INSU 5.

 - Neue und Größere Welt.
 - Neue Events wie z.B. Acid Rain.
 - Verbesserter Support von Teams.
 - Team Leben wird in der Hotbar angezeigt.
 - Bossbar mit Infos zum jeweiligen Event.
 - Team Balancing zum fairen aufteilen von Teams.
 - AntiXray (Versucht es garnicht :-) )

 ### Entwickler Neuerungen
- Benutzern der Software Paper 1.12.2 mit der Waterfall Proxy (1.13.2) inskulsive ViaVersion.
- Benutzen von Geyser und ViaVersion.
- Benutzen von MySQL Datenbanken.

## Einen Bug Melden

Falls du einen Bug melden möchtest, kannst du dies ganz bequem über unser Formular auf diesem Projekt unter issues. Auch ist es möglich uns über [Discord](https://discord.com/invite/eKgkSz7WQQ) direkt zu kontaktieren, ich bitte aber trotzdem, falls machbar das GitHub Formular zu benutzen, da andernfalls eine Bug-Beseitigung nicht sichergestellt werden kann.


## Ein Feature Vorschlagen

Du kannst relative einfach ein Feature vorschlagen. Gehe dazu einfach auf unseren [Discord](https://discord.com/invite/eKgkSz7WQQ) oder auf unseren [INSU Website](https://insu.the-flames.de) und fülle unten das Feature Formular aus.



## API's

Folgende API's wurden verwendet

| API | VERSION     | BESCHREIBUNG                | URL |
| :-------- | :------- | :------------------------- | :--|
| Java Discord API (JDA) | 5.0.0-beta.4 | Um Discord Bots in Java anzusteueren. | https://jda.wiki |
|Jitpack IO| LATEST| Library für viele Maven Anwendungen|https://jitpack.io/|

## Code Beispiele

**MYSQL:**

```java
public static List<String> getTeammateNamesbyPlayer(Player player) throws SQLException {
        Statement stmt = con.createStatement();

        ResultSet result = stmt.executeQuery("SELECT * FROM `INSU` WHERE `ID`='" + MYSQL.getIDbyName(player.getName()) + "';");

        result.first();

        List<String> players = new ArrayList<>();

        if (con.createStatement().executeQuery("SELECT * FROM `INSU` WHERE `ID`='" + MYSQL.getIDbyName(player.getName()) + "';").next()) {

            while (!result.isAfterLast()) {

                String team = result.getString("PLAYER");

                players.add(team);

                result.next();

                    if (players.contains("NONE")) {
                        players.remove("NONE");
                    }

                    if (players.contains(player.getName())) {
                        players.remove(player.getName());
                    }


            }
        }
        return players;
    }
```

```java
public static int getTeamIDbyName(String playername) throws SQLException {

        if (con.createStatement().executeQuery("SELECT ID FROM `INSU` WHERE PLAYER='" + playername + "';").next()) {

            try {
                Statement stmt = con.createStatement();

                ResultSet result = stmt.executeQuery("SELECT ID FROM `INSU` WHERE PLAYER='" + playername + "';");

                result.first();

                int team = result.getInt("ID");

                return team;

            } catch (SQLException e) {
                e.printStackTrace();
                return -403;
            }
        } else {
            return -404;
        }
    }
```

**Mute Command:**
```java
package INSU.creperozelot.commands;

import INSU.creperozelot.StaticCache;
import org.bukkit.Bukkit;
import org.bukkit.command.Command;
import org.bukkit.command.CommandExecutor;
import org.bukkit.command.CommandSender;

public class CommandMute implements CommandExecutor {
    @Override
    public boolean onCommand(CommandSender sender, Command command, String label, String[] args) {
        if (args.length != 1) {
            sender.sendMessage(StaticCache.prefix + "§Usage: /mute <player>");
        } else {
            if (Bukkit.getPlayer(args[0]) == null) {
                sender.sendMessage(StaticCache.prefix + "§Usage: Der Spieler " + args[0] + " §cwurde nicht gefunden!");
            } else if (StaticCache.muted_players.contains(args[0])) {
                sender.sendMessage(StaticCache.prefix + "§cDieser Spieler ist bereit gemuted!");
            } else {
                sender.sendMessage(StaticCache.prefix + "§aDu hast den Spieler §l " + args[0] + " §r§awurde gemuted.");
                StaticCache.muted_players.add(args[0]);
                Bukkit.getPlayer(args[0]).sendMessage(StaticCache.prefix + "§cDu wurdest gemuted!");
            }
        }
        return true;
    }
}

```

**Teamchest Command:**
```java
package INSU.creperozelot.commands;

import INSU.creperozelot.StaticCache;
import INSU.creperozelot.main;
import INSU.creperozelot.utils.MYSQL;
import INSU.creperozelot.utils.utils;
import org.bukkit.Bukkit;
import org.bukkit.command.Command;
import org.bukkit.command.CommandExecutor;
import org.bukkit.command.CommandSender;
import org.bukkit.entity.Player;
import org.bukkit.inventory.Inventory;

import java.io.IOException;
import java.sql.SQLException;
import java.util.ArrayList;
import java.util.List;

public class CommandTeamchest implements CommandExecutor {
    @Override
    public boolean onCommand(CommandSender sender, Command command, String s, String[] strings) {
        if (sender instanceof Player) {

            Player player = (Player) sender;


            try {
                List<String> teammatenames = new ArrayList<>(MYSQL.getTeammateNamesbyPlayer(player));
                List<Player> teammates = new ArrayList<>();
                if (!teammatenames.isEmpty()) {

                    for (String teammatenames2 : teammatenames) {
                        teammates.add(Bukkit.getPlayerExact(teammatenames2));
                    }

                    System.out.println(teammatenames);

                    for (Player allteammates : teammates) {
                        if (allteammates != null) {
                            if (allteammates.getOpenInventory().getTitle().contains("Teamchest")) {
                                player.sendMessage(StaticCache.prefix + "§cDein Teammate " + allteammates.getName() + "§c hat gerade die Teamchest offen.");
                                return true;
                            }
                        }
                    }

                    utils.openTeamChest(MYSQL.getTeamIDbyName(player.getName()), player);


                } else {
                    sender.sendMessage(StaticCache.prefix + main.getInstance().getConfig().getString("messages.errconsolerun"));
                }
            } catch (SQLException e) {
                throw new RuntimeException(e);
            } catch (IOException e) {
                throw new RuntimeException(e);
            }
        }
        return true;
    }
}

```

**Apply Discord Command:**
```java
package INSU.creperozelot.dc.bot.cmd;

import INSU.creperozelot.utils.MYSQL;
import INSU.creperozelot.utils.utils;
import net.dv8tion.jda.api.EmbedBuilder;
import net.dv8tion.jda.api.entities.Role;
import net.dv8tion.jda.api.entities.User;
import net.dv8tion.jda.api.events.interaction.command.SlashCommandInteractionEvent;

import java.awt.*;
import java.time.LocalDateTime;

public class CommandApply {
    public CommandApply(String applytype, User dcname, User dcnamepartner, String sender, String partner, String reason, SlashCommandInteractionEvent event) {
        if (utils.dchasrole(event.getMember(), "998618192257765376")) {

            User user = dcname;
            User user2 = dcnamepartner;

            switch (applytype) {
                case "accept":

                    EmbedBuilder eb = new EmbedBuilder();

                    eb.setTitle("Angenommen | Bewerbungs Status");

                    eb.setDescription("**Herzlichen Glückwunsch** \n Du " + dcname.getAsMention() + " **(" + sender + ")** und dein Teampartner **" + partner + "** wurden bei INSU Staffel 6 angenommen! \n\n Bitte denk dran noch niemanden zu erzählen, dass du mit deinen jeweiligen Teampartner-Teams, weil wir dies bis zur Veröffentlichung (18.03.23) **__nicht bekannt haben möchten!__** Bitte schicke zudem **__Toadenation#3561__** deinen und den Skin von deinem Teampartner über die Privatnachrichten Funktion von Discord (PN), weil Toad dies für den Content Bereich von INSU braucht.\n **__Bei Fragen bitte ich diese in dem Teilnehmer Chat von INSU stellen!__** \n\n **PS: Du und dein Teampartner müssten auf dem INSU Discord jetzt den Teilnehmer Rang haben!**");

                    eb.setTimestamp(LocalDateTime.now());

                    eb.setColor(Color.GREEN);

                    user.openPrivateChannel()
                            .flatMap(channel -> channel.sendMessageEmbeds(eb.build()))
                            .queue();

                    EmbedBuilder eb6 = new EmbedBuilder();

                    eb6.setTitle("Angenommen | Bewerbungs Status");

                    eb6.setDescription("**Herzlichen Glückwunsch** \n Du " + dcnamepartner.getAsMention() + " **(" + partner + ")** und dein Teampartner **" + sender + "** wurden bei INSU Staffel 6 angenommen! \n\n Bitte denk dran noch niemanden zu erzählen, dass du mit deinen jeweiligen Teampartner-Teams, weil wir dies bis zur Veröffentlichung (18.03.23) **__nicht bekannt haben möchten!__** Bitte schicke zudem **__Toadenation#3561__** deinen und den Skin von deinem Teampartner über die Privatnachrichten Funktion von Discord (PN), weil Toad dies für den Content Bereich von INSU braucht.\n **__Bei Fragen bitte ich diese in dem Teilnehmer Chat von INSU stellen!__** \n\n **PS: Du und dein Teampartner müssten auf dem INSU Discord jetzt den Teilnehmer Rang haben!**");

                    eb6.setTimestamp(LocalDateTime.now());

                    eb6.setColor(Color.GREEN);

                    user2.openPrivateChannel()
                            .flatMap(channel -> channel.sendMessageEmbeds(eb6.build()))
                            .queue();

                    Role teilnehmer = event.getGuild().getRoleById("998616111358685244");
                    event.getGuild().addRoleToMember(user, teilnehmer).queue();
                    event.getGuild().addRoleToMember(user2, teilnehmer).queue();

                    int id = MYSQL.getHighestID() + 1;

                    EmbedBuilder eb3 = new EmbedBuilder();

                    eb3.setTitle("Erfolg | Bewerbungs Status");

                    eb3.setDescription("Bewerbungs Status geändert! \n **Bitte führe nun den CMD /teamcreate aus und erstelle ein TEAM** \n **Empfohlene ID: **" + id);

                    eb3.setTimestamp(LocalDateTime.now());

                    eb3.setColor(Color.GREEN);

                    event.replyEmbeds(eb3.build()).setEphemeral(true).queue();


                    break;
                case "decline":

                    EmbedBuilder eb1 = new EmbedBuilder();

                    eb1.setTitle("Abgelehnt | Bewerbungs Status");

                    eb1.setDescription("**Es tut uns leid,** \n Du " + dcname.getAsMention() + " **(" + sender + ")** und dein Teampartner **" + partner + "** wurden bei INSU Staffel 6 abgelehnt! \n\n **Grund:** " + reason + "\n\n Bei Fragen bitte melde dich bei den **__Teilnehmermanagern__**:\n" +
                            "Entweder **__ᦔRollyLP#1700__** oder **__MaximalFlame#0327__** über die Privatnachrichten Funktion von Discord (PN)!\n" +
                            "\n" +
                            "Vielen Dank für dein Verständnis!");

                    eb1.setTimestamp(LocalDateTime.now());

                    eb1.setColor(Color.RED);

                    user.openPrivateChannel()
                            .flatMap(channel -> channel.sendMessageEmbeds(eb1.build()))
                            .queue();

                    EmbedBuilder eb5 = new EmbedBuilder();

                    eb5.setTitle("Abgelehnt | Bewerbungs Status");

                    eb5.setDescription("**Es tut uns leid,** \n Du " + dcnamepartner.getAsMention() + " **(" + partner + ")** und dein Teampartner **" + sender + "** wurden bei INSU Staffel 6 abgelehnt! \n\n **Grund:** " + reason + "\n\n Bei Fragen bitte melde dich bei den **__Teilnehmermanagern__**:\n" +
                                    "Entweder **__ᦔRollyLP#1700__** oder **__MaximalFlame#0327__** über die Privatnachrichten Funktion von Discord (PN)!\n" +
                                    "\n" +
                                    "Vielen Dank für dein Verständnis!");

                    eb5.setTimestamp(LocalDateTime.now());

                    eb5.setColor(Color.RED);

                    user2.openPrivateChannel()
                            .flatMap(channel -> channel.sendMessageEmbeds(eb5.build()))
                            .queue();

                    EmbedBuilder eb4 = new EmbedBuilder();

                    eb4.setTitle("Erfolg | Bewerbungs Status");

                    eb4.setDescription("Bewerbungs Status geändert!");

                    eb4.setTimestamp(LocalDateTime.now());

                    eb4.setColor(Color.GREEN);

                    event.replyEmbeds(eb4.build()).setEphemeral(true).queue();

                default:
                    EmbedBuilder eb2 = new EmbedBuilder();

                    eb2.setTitle("Fehler | Bewerbungs Status");

                    eb2.setDescription("Du musst bei applytype **accept/decline** eingeben!");

                    eb2.setTimestamp(LocalDateTime.now());

                    eb2.setColor(Color.RED);

                    event.replyEmbeds(eb2.build()).setEphemeral(true).queue();
                    break;
            }

        } else {
            event.reply(utils.permsmsgdc()).setEphemeral(true).queue();
        }
    }
}

```
**Autokick:**

```java
package INSU.creperozelot.tasks;

import INSU.creperozelot.main;
import org.bukkit.Bukkit;
import org.bukkit.entity.Player;
import org.bukkit.scheduler.BukkitRunnable;

import java.util.Date;

public class AutoKick {
    public static void run() {
        int hours = new Date().getHours();
        int minutes = new Date().getMinutes();
        int conf_hour = main.getInstance().getConfig().getInt("main.kicktime.hours");
        int conf_min = main.getInstance().getConfig().getInt("main.kicktime.minutes");
        new BukkitRunnable() {
            @Override
            public void run() {
                if (hours >= conf_hour && minutes >= conf_min) {
                    for (Player allonlineplayers : Bukkit.getOnlinePlayers()) {
                        allonlineplayers.kickPlayer(main.getInstance().getConfig().getString("messages.endtime"));
                    }
                }
            }
        }.runTaskTimer(main.getInstance(), 0, 20);
    }
}

```
