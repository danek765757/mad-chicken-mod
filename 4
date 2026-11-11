package ru.chickenking;

import net.fabricmc.api.ModInitializer;
import net.fabricmc.fabric.api.event.lifecycle.v1.ServerLifecycleEvents;
import net.fabricmc.fabric.api.event.lifecycle.v1.ServerTickEvents;
import net.fabricmc.fabric.api.event.player.PlayerBlockBreakEvents;
import net.fabricmc.fabric.api.event.player.UseBlockCallback;
import net.fabricmc.fabric.api.event.player.UseItemCallback;

import net.minecraft.block.Block;
import net.minecraft.block.BlockState;
import net.minecraft.item.BlockItem;
import net.minecraft.item.ItemPlacementContext;
import net.minecraft.item.ItemStack;
import net.minecraft.item.Items;
import net.minecraft.server.MinecraftServer;
import net.minecraft.server.network.ServerPlayerEntity;
import net.minecraft.server.world.ServerWorld;
import net.minecraft.text.Text;
import net.minecraft.util.ActionResult;
import net.minecraft.util.WorldSavePath;
import net.minecraft.util.math.BlockPos;
import net.minecraft.world.GameMode;

import java.io.InputStream;
import java.io.OutputStream;
import java.nio.file.Files;
import java.nio.file.Path;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.HashSet;
import java.util.Iterator;
import java.util.List;
import java.util.Map;
import java.util.Properties;
import java.util.Set;
import java.util.UUID;

public final class ChickenKingMod implements ModInitializer {
    private final Map<String, String> owners = new HashMap<>();
    private final Map<UUID, Jail> prisoners = new HashMap<>();
    private final List<PendingPlacement> pending = new ArrayList<>();
    private final Set<UUID> greeted = new HashSet<>();

    private Path saveFile;
    private boolean courtBuilt;
    private int ticks;
    private int seconds;
    private int decree;

    private record PendingPlacement(
            ServerWorld world,
            BlockPos pos,
            Block expected,
            BlockState before,
            String owner
    ) {}

    private static final class Jail {
        int left;
        final String mode;
        final String dimension;
        final double x;
        final double y;
        final double z;

        Jail(int left, String mode, String dimension,
             double x, double y, double z) {
            this.left = left;
            this.mode = mode;
            this.dimension = dimension;
            this.x = x;
            this.y = y;
            this.z = z;
        }

        String encode() {
            return left + "|" + mode + "|" + dimension
                    + "|" + x + "|" + y + "|" + z;
        }

        static Jail decode(String value) {
            String[] a = value.split("\\|");
            return new Jail(
                    Integer.parseInt(a[0]),
                    a[1],
                    a[2],
                    Double.parseDouble(a[3]),
                    Double.parseDouble(a[4]),
                    Double.parseDouble(a[5])
            );
        }
    }

    @Override
    public void onInitialize() {
        ServerLifecycleEvents.SERVER_STARTED.register(this::start);

        ServerLifecycleEvents.SERVER_STOPPED.register(server -> {
            save();
            owners.clear();
            prisoners.clear();
            pending.clear();
            greeted.clear();
        });

        ServerTickEvents.END_SERVER_TICK.register(this::tick);

        PlayerBlockBreakEvents.BEFORE.register(
                (world, player, pos, state, blockEntity) -> {
                    if (!(world instanceof ServerWorld sw)
                            || !(player instanceof ServerPlayerEntity sp)) {
                        return true;
                    }

                    if (prisoners.containsKey(sp.getUuid())) {
                        return false;
                    }

                    String owner = owners.get(key(sw, pos));
                    if (sp.getUuidAsString().equals(owner)) {
                        return true;
                    }

                    punish(
                            sw,
                            sp,
                            "Эти блоки принадлежат королю! "
                                    + "Ломать можно только собственные."
                    );
                    return false;
                }
        );

        PlayerBlockBreakEvents.AFTER.register(
                (world, player, pos, state, blockEntity) -> {
                    if (world instanceof ServerWorld sw) {
                        owners.remove(key(sw, pos));
                    }
                }
        );

        UseBlockCallback.EVENT.register(
                (player, world, hand, hit) -> {
                    if (!(world instanceof ServerWorld sw)
                            || !(player instanceof ServerPlayerEntity sp)) {
                        return ActionResult.PASS;
                    }

                    if (prisoners.containsKey(sp.getUuid())) {
                        return ActionResult.FAIL;
                    }

                    ItemStack stack = player.getStackInHand(hand);

                    if (stack.getItem() instanceof BlockItem blockItem) {
                        ItemPlacementContext context =
                                new ItemPlacementContext(
                                        player, hand, stack, hit
                                );

                        BlockPos pos = context.getBlockPos().toImmutable();

                        pending.add(new PendingPlacement(
                                sw,
                                pos,
                                blockItem.getBlock(),
                                sw.getBlockState(pos),
                                sp.getUuidAsString()
                        ));
                    }

                    return ActionResult.PASS;
                }
        );

        UseItemCallback.EVENT.register(
                (player, world, hand) -> {
                    if (!(world instanceof ServerWorld sw)
                            || !(player instanceof ServerPlayerEntity sp)) {
                        return ActionResult.PASS;
                    }

                    if (prisoners.containsKey(sp.getUuid())) {
                        return ActionResult.FAIL;
                    }

                    ItemStack stack = player.getStackInHand(hand);

                    if (decree == 0 && stack.isOf(Items.EGG)) {
                        punish(
                                sw,
                                sp,
                                "Запрещено бросать яйца! "
                                        + "Это потенциальные министры!"
                        );
                        return ActionResult.FAIL;
                    }

                    if (decree == 1 && stack.isOf(Items.CHICKEN)) {
                        punish(
                                sw,
                                sp,
                                "Запрещено есть сырую курятину! "
                                        + "Король оскорблён!"
                        );
                        return ActionResult.FAIL;
                    }

                    return ActionResult.PASS;
                }
        );
    }

    private void start(MinecraftServer server) {
        owners.clear();
        prisoners.clear();
        pending.clear();
        greeted.clear();

        ticks = 0;
        seconds = 0;
        decree = 0;
        courtBuilt = false;

        saveFile = server.getSavePath(WorldSavePath.ROOT)
                .resolve("chicken-king.properties");

        load();

        command(server,
                "execute in minecraft:overworld run "
                        + "forceload add -16 -16 16 16");

        if (!courtBuilt) {
            buildCourt(server);
            courtBuilt = true;
            save();
        }

        ensureKing(server);
    }

    private void buildCourt(MinecraftServer server) {
        String prefix = "execute in minecraft:overworld run ";

        command(server, prefix
                + "fill -10 200 -10 10 200 10 minecraft:stone_bricks");

        command(server, prefix
                + "fill -10 201 -10 10 206 10 minecraft:air");

        command(server, prefix
                + "fill -10 201 -10 10 201 -10 minecraft:oak_fence");
        command(server, prefix
                + "fill -10 201 10 10 201 10 minecraft:oak_fence");
        command(server, prefix
                + "fill -10 201 -10 -10 201 10 minecraft:oak_fence");
        command(server, prefix
                + "fill 10 201 -10 10 201 10 minecraft:oak_fence");

        command(server, prefix
                + "fill -1 201 4 1 201 6 minecraft:gold_block");
        command(server, prefix
                + "fill -1 202 6 1 204 6 minecraft:gold_block");

        command(server, prefix
                + "fill 4 201 -2 8 205 2 minecraft:iron_bars");
        command(server, prefix
                + "fill 5 202 -1 7 204 1 minecraft:air");
        command(server, prefix
                + "fill 4 205 -2 8 205 2 minecraft:glass");

        // Несколько зрителей перед клеткой.
        for (int x = -3; x <= 1; x += 2) {
            command(server, prefix
                    + "summon minecraft:villager "
                    + x + " 201 -4 "
                    + "{NoAI:1b,Invulnerable:1b,"
                    + "PersistenceRequired:1b,"
                    + "Tags:[\"ck_audience\"]}");
        }
    }

    private void ensureKing(MinecraftServer server) {
        command(server,
                "execute in minecraft:overworld "
                        + "unless entity @e[tag=ck_king] run "
                        + "summon minecraft:chicken 0.5 202 5.5 "
                        + "{NoAI:1b,Invulnerable:1b,"
                        + "PersistenceRequired:1b,"
                        + "Tags:[\"ck_king\"],"
                        + "CustomName:{text:\"Король Кудах I\","
                        + "color:\"gold\",bold:true},"
                        + "CustomNameVisible:1b}");
    }

    private void tick(MinecraftServer server) {
        ticks++;

        // Проверяем фактический результат установки блока.
        for (PendingPlacement placement : pending) {
            BlockState current =
                    placement.world().getBlockState(placement.pos());

            if (!current.equals(placement.before())
                    && current.isOf(placement.expected())) {
                owners.put(
                        key(placement.world(), placement.pos()),
                        placement.owner()
                );
            }
        }
        pending.clear();

        if (ticks % 20 != 0) {
            return;
        }

        seconds++;

        Set<UUID> online = new HashSet<>();

        for (ServerPlayerEntity player :
                server.getPlayerManager().getPlayerList()) {
            online.add(player.getUuid());

            if (greeted.add(player.getUuid())) {
                tell(player,
                        "§6[Король Кудах] §fТеперь ты подданный! "
                                + "Чужие и природные блоки ломать нельзя. "
                                + "Свои — можно.");
                tell(player, "§e" + currentDecree());
            }
        }

        greeted.retainAll(online);

        Iterator<Map.Entry<UUID, Jail>> iterator =
                prisoners.entrySet().iterator();

        while (iterator.hasNext()) {
            Map.Entry<UUID, Jail> entry = iterator.next();
            ServerPlayerEntity player =
                    server.getPlayerManager().getPlayer(entry.getKey());

            // Пока игрок не в сети, наказание приостановлено.
            if (player == null) {
                continue;
            }

            Jail jail = entry.getValue();
            String name = player.getName().getString();

            if (jail.left <= 0) {
                command(server,
                        "execute in " + jail.dimension
                                + " run tp " + name + " "
                                + jail.x + " " + jail.y + " " + jail.z);
                command(server, "gamemode " + jail.mode + " " + name);

                tell(player,
                        "§aТы свободен! Король милостиво "
                                + "разрешил тебе существовать.");
                iterator.remove();
                continue;
            }

            command(server, "gamemode adventure " + name);
            command(server,
                    "execute in minecraft:overworld run tp "
                            + name + " 6.5 202 0.5");

            if (jail.left == 30 || jail.left % 10 == 0
                    || jail.left <= 3) {
                tell(player,
                        "§cВ клетке осталось: "
                                + jail.left + " сек.");
            }

            jail.left--;
        }

        if (seconds % 300 == 0) {
            decree = 1 - decree;
            broadcast(server, "§6[Новый указ] §e" + currentDecree());
        }

        if (seconds % 60 == 0) {
            ensureKing(server);
        }

        if (seconds % 5 == 0) {
            save();
        }
    }

    private void punish(ServerWorld world,
                        ServerPlayerEntity player,
                        String reason) {
        if (prisoners.containsKey(player.getUuid())) {
            return;
        }

        GameMode mode = player.interactionManager.getGameMode();

        String previousMode =
                mode == GameMode.CREATIVE ? "creative"
                        : mode == GameMode.ADVENTURE ? "adventure"
                        : mode == GameMode.SPECTATOR ? "spectator"
                        : "survival";

        prisoners.put(
                player.getUuid(),
                new Jail(
                        30,
                        previousMode,
                        world.getRegistryKey().getValue().toString(),
                        player.getX(),
                        player.getY(),
                        player.getZ()
                )
        );

        MinecraftServer server = world.getServer();
        String name = player.getName().getString();

        broadcast(server,
                "§6[Королевский суд] §c" + name
                        + " отправляется в клетку на 30 секунд! §f"
                        + reason);

        command(server, "gamemode adventure " + name);
        command(server,
                "execute in minecraft:overworld run tp "
                        + name + " 6.5 202 0.5");

        save();
    }

    private String currentDecree() {
        return decree == 0
                ? "Указ: нельзя бросать яйца. "
                    + "Яйца — будущие министры!"
                : "Указ: нельзя есть сырую курятину. "
                    + "Король считает это государственной грубостью!";
    }

    private static String key(ServerWorld world, BlockPos pos) {
        return world.getRegistryKey().getValue()
                + "/" + pos.getX()
                + "/" + pos.getY()
                + "/" + pos.getZ();
    }

    private static void command(MinecraftServer server,
                                String command) {
        server.getCommandManager().executeWithPrefix(
                server.getCommandSource(),
                command
        );
    }

    private static void tell(ServerPlayerEntity player,
                             String message) {
        player.sendMessage(Text.literal(message), false);
    }

    private static void broadcast(MinecraftServer server,
                                  String message) {
        for (ServerPlayerEntity player :
                server.getPlayerManager().getPlayerList()) {
            tell(player, message);
        }
    }

    private void load() {
        if (saveFile == null || !Files.exists(saveFile)) {
            return;
        }

        Properties properties = new Properties();

        try (InputStream input = Files.newInputStream(saveFile)) {
            properties.load(input);

            courtBuilt = Boolean.parseBoolean(
                    properties.getProperty("courtBuilt", "false")
            );

            for (String property : properties.stringPropertyNames()) {
                if (property.startsWith("owner.")) {
                    owners.put(
                            property.substring(6),
                            properties.getProperty(property)
                    );
                } else if (property.startsWith("jail.")) {
                    try {
                        UUID uuid = UUID.fromString(
                                property.substring(5)
                        );
                        prisoners.put(
                                uuid,
                                Jail.decode(properties.getProperty(property))
                        );
                    } catch (RuntimeException exception) {
                        System.err.println(
                                "[ChickenKing] Bad jail entry: " + property
                        );
                    }
                }
            }
        } catch (Exception exception) {
            System.err.println(
                    "[ChickenKing] Could not load saved data"
            );
            exception.printStackTrace();
        }
    }

    private void save() {
        if (saveFile == null) {
            return;
        }

        Properties properties = new Properties();
        properties.setProperty(
                "courtBuilt",
                Boolean.toString(courtBuilt)
        );

        owners.forEach((position, owner) ->
                properties.setProperty("owner." + position, owner));

        prisoners.forEach((uuid, jail) ->
                properties.setProperty(
                        "jail." + uuid,
                        jail.encode()
                ));

        try {
            Files.createDirectories(saveFile.getParent());

            Path temporary = saveFile.resolveSibling(
                    saveFile.getFileName() + ".tmp"
            );

            try (OutputStream output =
                         Files.newOutputStream(temporary)) {
                properties.store(output, "Chicken King save data");
            }

            Files.move(
                    temporary,
                    saveFile,
                    java.nio.file.StandardCopyOption.REPLACE_EXISTING
            );
        } catch (Exception exception) {
            System.err.println(
                    "[ChickenKing] Could not save data"
            );
            exception.printStackTrace();
        }
    }
}
