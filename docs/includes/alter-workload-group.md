  變更現有的資源管理員工作負載群組設定，並選擇性地將其指派給資源管理員資源集區。  
  
 ![主題連結圖示](../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)。  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
ALTER WORKLOAD GROUP { group_name | "default" }  
[ WITH  
    ([ IMPORTANCE = { LOW | MEDIUM | HIGH } ]  
      [ [ , ] REQUEST_MAX_MEMORY_GRANT_PERCENT = value ]  
      [ [ , ] REQUEST_MAX_CPU_TIME_SEC = value ]  
      [ [ , ] REQUEST_MEMORY_GRANT_TIMEOUT_SEC = value ]   
      [ [ , ] MAX_DOP = value ]  
      [ [ , ] GROUP_MAX_REQUESTS = value ] )  
 ]  
[ USING { pool_name | "default" } ]  
[ ; ]  
```  
  
## <a name="arguments"></a>引數

 *group_name* | "**default**"       
 這是現有使用者定義之工作負載群組的名稱，或是資源管理員預設工作負載群組的名稱。  
  
> [!NOTE]  
> 當您安裝 [!INCLUDE[ssNoVersion](ssnoversion-md.md)] 時，資源管理員就會建立此 "default" 和內部群組。  
  
 搭配 ALTER WORKLOAD GROUP 使用時，"default" 選項必須加上引號 ("") 或方括號 ([]) 才能避免與系統保留字 DEFAULT 產生衝突。 如需詳細資訊，請參閱＜ [Database Identifiers](../relational-databases/databases/database-identifiers.md)＞。  
  
> [!NOTE]  
> 預先定義的工作負載群組和資源集區都會使用小寫名稱，例如 "default"。 如果是使用區分大小寫之定序的伺服器，則應該將此列入考量。 具有不區分大小寫之定序 (如 SQL_Latin1_General_CP1_CI_AS) 的伺服器會將 "default" 和 "Default" 視為相同。  
  
 IMPORTANCE = { LOW | **MEDIUM** | HIGH }       
 指定要求在工作負載群組中的相對重要性。 重要性為下列其中一項：  
  
-   LOW  
-   MEDIUM (預設值)  
-   HIGH  
  
> [!NOTE]  
> 每個重要性設定在內部都會儲存為計算所使用的數字。  
  
 IMPORTANCE 的資源集區範圍為本機；相同資源集區內部不同重要性的工作負載群組會彼此影響，但不會影響另一個資源集區中的工作負載群組。  
  
 REQUEST_MAX_MEMORY_GRANT_PERCENT = *value*     
 指定單一要求可由集區中獲取的記憶體最大數量。 *value* 是相對於 MAX_MEMORY_PERCENT 所指定的資源集區大小的百分比。  

*value* 是最高到 [!INCLUDE[ssSQL17](sssql17-md.md)] 的整數、從 [!INCLUDE[sql-server-2019](sssqlv15-md.md)] 開始的浮點數。 預設值為 25。 允許的 *value* 範圍為 1 至 100。
  
> [!NOTE]  
> 指定的數量僅參考查詢執行授與記憶體。  
  
> [!IMPORTANT]
> 將 *value* 設定為 0 會避免在使用者定義的工作負載群組中，執行具有 SORT 和 HASH JOIN 作業的查詢。     
>
> 不建議您將 *value* 設定為大於 70，因為如果其他並行查詢正在執行，伺服器可能無法將足夠的記憶體擱置在一旁。 最後，這可能會導致查詢逾時錯誤 8645。      
  
> [!NOTE]  
> 如果查詢記憶體需求超過這個參數所指定的限制，伺服器會執行以下作業：  
>   
> -  如果是使用者定義的工作負載群組，伺服器會嘗試減少查詢的平行處理原則程度，直到記憶體需求低於此限制，或是直到平行處理原則程度等於 1 為止。 如果查詢記憶體需求仍然大於此限制，將會發生錯誤 8657。  
>   
> -  如果是內部和預設的工作負載群組，伺服器會允許查詢取得所需的記憶體。  
>   
> 請注意，如果伺服器沒有足夠的實體記憶體，這兩種情況都會受限於逾時錯誤 8645。  
  
 REQUEST_MAX_CPU_TIME_SEC = *value*       
 指定要求可以使用的最大 CPU 時間量 (以秒為單位)。 *value* 必須是 0 或正整數。 *value* 的預設設定為 0，這代表沒有限制。  
  
> [!NOTE]  
> 根據預設，Resource Governor 不會在超過最大時間時，阻止要求繼續執行。 不過，系統將會產生某個事件。 如需詳細資訊，請參閱[超過 CPU 閾值事件類別](../relational-databases/event-classes/cpu-threshold-exceeded-event-class.md)。 

> [!IMPORTANT]
> 從 [!INCLUDE[ssSQL15](sssql16-md.md)] SP2 和 [!INCLUDE[ssSQL17](sssql17-md.md)] CU3 開始，並使用[追蹤旗標 2422](../t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql.md)，Resource Governor 將在超過時間上限時中止要求。
  
 REQUEST_MEMORY_GRANT_TIMEOUT_SEC =*value*  
 指定查詢能夠等待記憶體授權 (工作緩衝區記憶體) 變成可用的最大時間 (以秒為單位)。  
  
> [!NOTE]  
> 到達記憶體授權的逾時值時，查詢不一定會失敗。 只有當有太多並行的查詢正在執行時，查詢才會失敗。 否則，查詢可能只會得到最小的記憶體授權，導致查詢效能降低。  
  
 *value* 必須為正整數。 *value* 的預設設定 0 會根據查詢成本使用內部計算來判斷最大時間。  
  
 MAX_DOP =*value*       
 為平行要求指定平行處理原則的最大程度 (DOP)。 *value* 必須是 0 或正整數 (1 到 255)。 當 *value* 為 0 時，伺服器會選擇平行處理原則的最大程度。 這是預設值且為建議的設定。  
  
> [!NOTE]  
> [!INCLUDE[ssDE](ssde-md.md)] 針對 MAX_DOP 所設定的實際值可能會小於指定的值。 最終的值是由公式 min(255, *CPU 的數目)* 所決定。  
  
> [!CAUTION]  
> 變更 MAX_DOP 可能會對伺服器的效能造成不良影響。 如果您必須變更 MAX_DOP，我們建議您將它設定為小於或等於存在單一 NUMA 節點中之最大硬體排程器數目的值。 我們建議您不要將 MAX_DOP 設定為大於 8 的值。  
  
 MAX_DOP 會以下列方式處理：  
  
-   只要 MAX_DOP 沒有超過工作負載群組 MAX_DOP，就會接受當做查詢提示的 MAX_DOP。  
  
-   當做查詢提示的 MAX_DOP 永遠會覆寫 sp_configure 的「平行處理原則的最大程度」。  
  
-   工作負載群組 MAX_DOP 會覆寫 sp_configure 的「平行處理原則的最大程度」。  
  
-   如果查詢在編譯時間被標示為序列 (MAX_DOP = 1)，則不管工作負載群組或 sp_configure 設定為何，都無法在執行階段將該查詢變更回平行。  
  
 DOP 經過設定後，在授與記憶體不足的壓力下，僅能將其降低。 在授與記憶體佇列中等候時，看不到工作負載群組的重新組態。  
  
 GROUP_MAX_REQUESTS = *value*      
 指定在工作負載群組中可允許執行的最大同時要求數。 *value* 必須是 0 或正整數。 *value* 的預設設定 0 允許無限制的要求。 達到最大並行要求時，該群組中的使用者可以登入，但是會處於等候狀態，直到並行要求低於指定的值為止。  
  
 USING { *pool_name* | "**default**" }      
 將工作負載群組與 *pool_name* 所識別的使用者定義資源集區產生關聯，實際上會將工作負載群組放入資源集區中。 如果未提供 *pool_name* 或未使用 USING 引數，工作負載群組會放入預先定義的資源管理員預設集區。  
  
 搭配 ALTER WORKLOAD GROUP 使用時，"default" 選項必須加上引號 ("") 或方括號 ([]) 才能避免與系統保留字 DEFAULT 產生衝突。 如需詳細資訊，請參閱＜ [Database Identifiers](../relational-databases/databases/database-identifiers.md)＞。  
  
> [!NOTE]  
> "default" 選項會區分大小寫。  
  
## <a name="remarks"></a>備註  
 在預設群組中允許使用 ALTER WORKLOAD GROUP。  
  
 工作負載群組組態的變更在執行 ALTER RESOURCE GOVERNOR RECONFIGURE 前不會生效。 當變更計劃影響設定時，只有在執行 DBCC FREEPROCCACHE (*pool_name*) 之後新的設定才會在先前已快取的計劃中生效，其中 *pool_name* 是工作負載群組相關聯之 Resource Governor 資源集區的名稱。  
  
-   如果將 MAX_DOP 變更為 1，則不需要執行 DBCC FREEPROCCACHE 因為平行計畫可以在序列模式中執行。 不過，它可能不會和編譯為序列計劃的計劃一樣有效率。  
  
-   如果將 MAX_DOP 從 1 變更為 0 或大於 1 的值，則不需要執行 DBCC FREEPROCCACHE。 不過，序列計畫無法以平行方式執行，因此清除個別的快取將會允許新計劃有可能使用平行處理原則進行編譯。  
  
> [!CAUTION]  
> 從與超過一個工作負載群組相關聯的資源集區清除已快取的計劃，會影響包含以 *pool_name* 所識別使用者定義資源集區的所有工作負載群組。  
  
 要執行 DDL 陳述式時，建議您先熟悉資源管理員的狀態。 如需詳細資訊，請參閱 [Resource Governor](../relational-databases/resource-governor/resource-governor.md)。  
  
 REQUEST_MEMORY_GRANT_PERCENT：在 [!INCLUDE[ssVersion2005](ssversion2005-md.md)] 中，允許建立索引即可使用比一開始授與的記憶體更多工作區記憶體來改善效能。 資源管理員的更新版本中支援此特殊處理，不過，初始授與和任何額外的記憶體授與都會受到資源集區和工作負載群組設定的限制。  
  
 **在資料分割資料表上建立索引**  
  
 非對齊式分割區資料表上之索引建立所耗用的記憶體，與相關的分割區數目成正比。  如果所需的總記憶體超出資源管理員工作負載群組設定所設的每個查詢限制 (REQUEST_MAX_MEMORY_GRANT_PERCENT)，這個索引建立動作就可能無法執行。 由於 "default" 工作負載群組允許查詢超過每個查詢限制，而且具有 [!INCLUDE[ssVersion2005](ssversion2005-md.md)] 相容性啟動所需的記憶體下限，因此使用者或許能夠在 "default" 工作負載群組中執行相同的索引建立動作，但前提是 "default" 資源集區有設定足夠的總記憶體來執行這類查詢。  
  
## <a name="permissions"></a>權限  
 需要 `CONTROL SERVER` 權限。  
  
## <a name="examples"></a>範例  
 下列範例顯示如何將預設群組中的要求重要性從 `MEDIUM` 變更為 `LOW`。  
  
```sql  
ALTER WORKLOAD GROUP "default"  
WITH (IMPORTANCE = LOW);  
GO  
ALTER RESOURCE GOVERNOR RECONFIGURE;  
GO  
```  
  
 下列範例顯示如何將工作負載群組從所屬集區移到預設集區。  
  
```sql  
ALTER WORKLOAD GROUP adHoc  
USING [default];  
GO  
ALTER RESOURCE GOVERNOR RECONFIGURE;  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [資源管理員](../relational-databases/resource-governor/resource-governor.md)   
 [CREATE WORKLOAD GROUP &#40;Transact-SQL&#41;](../t-sql/statements/create-workload-group-transact-sql.md)   
 [DROP WORKLOAD GROUP &#40;Transact-SQL&#41;](../t-sql/statements/drop-workload-group-transact-sql.md)   
 [CREATE RESOURCE POOL &#40;Transact-SQL&#41;](../t-sql/statements/create-resource-pool-transact-sql.md)   
 [ALTER RESOURCE POOL &#40;Transact-SQL&#41;](../t-sql/statements/alter-resource-pool-transact-sql.md)   
 [DROP RESOURCE POOL &#40;Transact-SQL&#41;](../t-sql/statements/drop-resource-pool-transact-sql.md)   
 [ALTER RESOURCE GOVERNOR &#40;Transact-SQL&#41;](../t-sql/statements/alter-resource-governor-transact-sql.md)  
