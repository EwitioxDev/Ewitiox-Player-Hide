package de.ewitiox.playerhide;

import org.bukkit.Bukkit;
import org.bukkit.Material;
import org.bukkit.Server;
import org.bukkit.command.Command;
import org.bukkit.command.CommandSender;
import org.bukkit.entity.HumanEntity;
import org.bukkit.entity.Player;
import org.bukkit.event.EventHandler;
import org.bukkit.event.Listener;
import org.bukkit.event.block.Action;
import org.bukkit.event.inventory.InventoryClickEvent;
import org.bukkit.event.player.PlayerInteractEvent;
import org.bukkit.inventory.Inventory;
import org.bukkit.inventory.InventoryView;
import org.bukkit.inventory.ItemStack;
import org.bukkit.inventory.PlayerInventory;
import org.bukkit.inventory.meta.ItemMeta;
import org.bukkit.plugin.PluginManager;
import org.bukkit.plugin.java.JavaPlugin;

public class Main
  extends JavaPlugin
  implements Listener
{
  public static Main instance;
  public Inventory inventory = null;
  
  public void onEnable()
  {
    Bukkit.getServer().getPluginManager().registerEvents(this, this);
  }
  
  public boolean onCommand(CommandSender sender, Command cmd, String label, String[] args)
  {
    if (cmd.getLabel().equalsIgnoreCase("ph")) {
      if (sender.hasPermission("ph.item"))
      {
        ItemStack h = new ItemStack(Material.BLAZE_ROD);
        ItemMeta hmeta = h.getItemMeta();
        hmeta.setDisplayName("§6Spieler Verstecken §7(Rechtsklick)");
        h.setItemMeta(hmeta);
        if ((sender instanceof Player)) {
          ((Player)sender).getInventory().addItem(new ItemStack[] { h });
        } else {
          sender.sendMessage("§cDu musst ein Spieler sein!");
        }
      }
      else
      {
        sender.sendMessage("§cDir fehlt das Recht §7'ph.item'");
      }
    }
    return true;
  }
  
  @EventHandler
  public void onPlayerInteract(PlayerInteractEvent e)
  {
    Player p = e.getPlayer();
    
    ItemStack item1 = new ItemStack(Material.STAINED_GLASS_PANE, 1, (short)5);
    ItemMeta meta1 = item1.getItemMeta();
    meta1.setDisplayName("§a§lSpieler anzeigen");
    item1.setItemMeta(meta1);
    
    ItemStack item2 = new ItemStack(Material.STAINED_GLASS_PANE, 1, (short)14);
    ItemMeta meta2 = item2.getItemMeta();
    meta2.setDisplayName("§c§lSpieler verstecken");
    item2.setItemMeta(meta2);
    if ((((e.getAction() == Action.RIGHT_CLICK_AIR ? 1 : 0) | (e.getAction() == Action.RIGHT_CLICK_BLOCK ? 1 : 0)) != 0) && 
      (e.getMaterial().equals(Material.BLAZE_ROD)))
    {
      this.inventory = p.getPlayer().getServer().createInventory(null, 9, "§5Spieler Verstecken §8| §aCode by Ewitiox");
      
      this.inventory.setItem(2, item1);
      this.inventory.setItem(6, item2);
      
      p.getPlayer().openInventory(this.inventory);
    }
  }
  
  @EventHandler
  public void Inventory(InventoryClickEvent e)
  {
    HumanEntity p = e.getWhoClicked();
    if (e.getCurrentItem().getType() == Material.STAINED_GLASS_PANE) {
      if (e.getCurrentItem().getItemMeta().getDisplayName().matches("§c§lSpieler verstecken"))
      {
        for (Player all : Bukkit.getOnlinePlayers())
        {
          Player p1 = (Player)e.getWhoClicked();
          p1.hidePlayer(all);
        }
        e.getView().close();
      }
      else if (e.getCurrentItem().getItemMeta().getDisplayName().matches("§a§lSpieler anzeigen"))
      {
        for (Player all : Bukkit.getOnlinePlayers())
        {
          Player p1 = (Player)e.getWhoClicked();
          p1.showPlayer(all);
        }
        e.getView().close();
      }
    }
  }
}
