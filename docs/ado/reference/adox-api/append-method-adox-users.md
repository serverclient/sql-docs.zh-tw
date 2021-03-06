---
description: Append 方法 (ADOX Users)
title: 將方法附加 (ADOX 使用者) |Microsoft Docs
ms.prod: sql
ms.prod_service: connectivity
ms.technology: ado
ms.custom: ''
ms.date: 01/19/2017
ms.reviewer: ''
ms.topic: conceptual
apitype: COM
f1_keywords:
- Users::raw_Append
- Users::Append
helpviewer_keywords:
- Append method [ADOX]
ms.assetid: b80bc5d5-78ca-4f75-956b-2ac658029cc7
author: rothja
ms.author: jroth
ms.openlocfilehash: 14b0c573b3ccf8a03b1c2f6513cdac67303fb4bf
ms.sourcegitcommit: 18a98ea6a30d448aa6195e10ea2413be7e837e94
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "88985429"
---
# <a name="append-method-adox-users"></a>Append 方法 (ADOX Users)
將新的 [使用者](./user-object-adox.md) 物件加入至 [Users](./users-collection-adox.md) 集合。  
  
## <a name="syntax"></a>語法  
  
```  
  
Users.Append User[,Password]  
```  
  
#### <a name="parameters"></a>參數  
 *User*  
 **Variant**值，包含要附加的**使用者**物件，或是要建立和附加的使用者名稱。  
  
 *密碼*  
 選擇性。 包含使用者密碼的 **字串** 值。 *Password*參數會對應至**使用者**物件的[ChangePassword](./changepassword-method-adox.md)方法所指定的值。  
  
## <a name="remarks"></a>備註  
 [目錄](./catalog-object-adox.md)的**users**集合代表所有目錄的使用者。 [群組](./group-object-adox.md)的**users**集合只代表具有特定群組成員資格的使用者。  
  
 如果提供者不支援建立使用者，就會發生錯誤。  
  
> [!NOTE]
>  將**使用者**物件附加至**群組**物件的**Users**集合之前，**類別目錄**的**users**集合中必須已經有一個**使用者**物件與要附加之名稱相同的[名稱](./name-property-adox.md)。  
  
## <a name="applies-to"></a>套用至  
 [Users 集合 (ADOX)](./users-collection-adox.md)  
  
## <a name="see-also"></a>另請參閱  
 [群組和使用者附加、ChangePassword 方法範例 (VB) ](./groups-and-users-append-changepassword-methods-example-vb.md)   
 [將方法附加至 ADOX 資料行 () ](./append-method-adox-columns.md)   
 [將方法附加至 ADOX 群組 () ](./append-method-adox-groups.md)   
 [附加方法 (ADOX 索引) ](./append-method-adox-indexes.md)   
 [附加方法 (ADOX 索引鍵) ](./append-method-adox-keys.md)   
 [Append 方法 (ADOX 程式) ](./append-method-adox-procedures.md)   
 [附加方法 (ADOX 資料表) ](./append-method-adox-tables.md)   
 [Append 方法 (ADOX Views)](./append-method-adox-views.md)