# Code Examples

## Complete Item System Example

```csharp
using Glitch9.AIDevKit.Sheets;
using UnityEngine;

public class ItemManager : MonoBehaviour
{
    private ItemTable m_ItemTable;

    void Start()
    {
        // Load item table
        m_ItemTable = SheetDatabase.Load<ItemTable>("Items");
        
        if (m_ItemTable == null)
        {
            Debug.LogError("Failed to load Items table");
            return;
        }

        // Get specific item
        DisplayItem("apple");
    }

    void DisplayItem(string itemKey)
    {
        var row = m_ItemTable.GetRow(itemKey);
        
        if (row == null)
        {
            Debug.LogWarning($"Item not found: {itemKey}");
            return;
        }

        string name = row.GetString("Name_EN");
        string desc = row.GetString("Description_EN");
        int price = row.GetInt("Price");
        float weight = row.GetFloat("Weight");
        
        Debug.Log($"{name} - ${price}");
        Debug.Log($"Weight: {weight}kg");
        Debug.Log($"Description: {desc}");
    }
}
```

## Shop System Example

```csharp
public class ShopSystem : MonoBehaviour
{
    private ItemTable m_ItemTable;

    void Start()
    {
        m_ItemTable = SheetDatabase.Load<ItemTable>("Items");
        DisplayShopItems();
    }

    void DisplayShopItems()
    {
        // Get all purchasable items
        var shopItems = m_ItemTable.GetRows()
            .Where(row => row.GetBool("CanPurchase", false))
            .Where(row => row.GetInt("Price") > 0)
            .OrderBy(row => row.GetInt("Price"));

        Debug.Log("=== SHOP ===");
        
        foreach (var item in shopItems)
        {
            string name = item.GetString("Name_EN");
            int price = item.GetInt("Price");
            Debug.Log($"{name}: ${price}");
        }
    }

    public bool PurchaseItem(string itemKey, int playerGold)
    {
        var row = m_ItemTable.GetRow(itemKey);
        
        if (row == null) return false;
        
        int price = row.GetInt("Price");
        
        if (playerGold >= price)
        {
            // Purchase successful
            return true;
        }
        
        return false;
    }
}
```

## Enemy Database Example

```csharp
public class EnemySpawner : MonoBehaviour
{
    private EnemyTable m_EnemyTable;

    void Start()
    {
        m_EnemyTable = SheetDatabase.Load<EnemyTable>("Enemies");
    }

    public void SpawnEnemy(string enemyKey)
    {
        var row = m_EnemyTable.GetRow(enemyKey);
        
        if (row == null) return;

        // Create enemy instance
        GameObject enemy = new GameObject(row.GetString("Name"));
        
        // Set enemy stats
        var stats = enemy.AddComponent<EnemyStats>();
        stats.maxHealth = row.GetInt("Health");
        stats.attack = row.GetInt("Attack");
        stats.defense = row.GetInt("Defense");
        stats.speed = row.GetFloat("Speed");
        stats.level = row.GetInt("Level");
        
        Debug.Log($"Spawned {stats.name} - Level {stats.level}");
    }

    public void SpawnRandomEnemy(int playerLevel)
    {
        // Get enemies within player level range
        var suitableEnemies = m_EnemyTable.GetRows()
            .Where(row => 
            {
                int enemyLevel = row.GetInt("Level");
                return enemyLevel >= playerLevel - 2 && 
                       enemyLevel <= playerLevel + 2;
            })
            .ToList();

        if (suitableEnemies.Count == 0) return;

        // Spawn random enemy
        var random = suitableEnemies[Random.Range(0, suitableEnemies.Count)];
        SpawnEnemy(random.Key);
    }
}
```

## Quest System Example

```csharp
public class QuestManager : MonoBehaviour
{
    private QuestTable m_QuestTable;

    void Start()
    {
        m_QuestTable = SheetDatabase.Load<QuestTable>("Quests");
    }

    public void StartQuest(string questKey)
    {
        var row = m_QuestTable.GetRow(questKey);
        
        if (row == null) return;

        string title = row.GetString("Title");
        string description = row.GetString("Description");
        int level = row.GetInt("RequiredLevel");
        int reward = row.GetInt("RewardGold");

        Debug.Log($"Quest Started: {title}");
        Debug.Log($"Required Level: {level}");
        Debug.Log($"Reward: {reward} gold");
        Debug.Log($"Description: {description}");
    }

    public List<string> GetAvailableQuests(int playerLevel)
    {
        return m_QuestTable.GetRows()
            .Where(row => row.GetInt("RequiredLevel") <= playerLevel)
            .Where(row => !row.GetBool("IsCompleted", false))
            .Select(row => row.Key)
            .ToList();
    }
}
```
