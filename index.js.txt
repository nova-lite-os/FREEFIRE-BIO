const { Client, GatewayIntentBits, Events } = require('discord.js');
require('dotenv').config();

const client = new Client({
  intents: [
    GatewayIntentBits.Guilds,
    GatewayIntentBits.GuildMembers
  ]
});

const TARGETS = [100, 500, 1000];

client.once(Events.ClientReady, async () => {
  console.log(`✅ Bot online: ${client.user.tag}`);

  const guild = await client.guilds.fetch(process.env.GUILD_ID);
  updateMemberCount(guild);

  client.on(Events.GuildMemberAdd, () => updateMemberCount(guild));
  client.on(Events.GuildMemberRemove, () => updateMemberCount(guild));
});

async function updateMemberCount(guild) {
  const count = (await guild.fetch()).memberCount;
  client.user.setActivity(`👥 ${count} Members`, { type: 3 });

  if (TARGETS.includes(count)) {
    const channel = guild.systemChannel || guild.channels.cache.find(c => c.isTextBased());
    if (channel) channel.send(`🎉 مبروك! وصلنا إلى ${count} عضو!`);
  }
}

client.login(process.env.TOKEN);
