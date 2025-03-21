---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: db05ce33a9fb602d6d48d1a1606f48a7fbde246e
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55888674"
---
| 限制識別碼 | 限制 | 註解 |
| --- | --- | --- |
| 儲存體帳戶認證的數目上限 |64 | |
| 磁碟區容器的數目上限 |64 | |
| 磁碟區的數目上限 |255 | |
| 每個頻寬範本之排程的數目上限 |168 |每週每天 (24*7)、每小時的排程。 |
| 實體裝置上之分層磁碟區的大小上限 |64 TB (適用於 8100 和 8600) |8100 和 8600 為實體裝置。 |
| Azure 中的虛擬裝置上之分層磁碟區的大小上限 |30 TB (適用於 8010)  <br></br>  64 TB (適用於 8020) |8010 和 8020 為 Azure 中的虛擬裝置，分別使用了「標準儲存體」和「進階儲存體」。 |
| 實體裝置上之固定在本機的磁碟區的大小上限 |9 TB (適用於 8100)  <br></br>  24 TB (適用於 8600) |8100 和 8600 為實體裝置。 |
| iSCSI 連線的數目上限 |512 | |
| 從起始端之 iSCSI 連線的數目上限 |512 | |
| 每個裝置的存取控制記錄的數目上限 |64 | |
| 每個備份原則的磁碟區數目上限 |24 | |
| 每個備份原則保留的備份數目上限 |64 | |
| 每個備份原則的排程數目上限 |10 | |
| 每個磁碟區可以保留之任何類型快照集的數目上限 |256 |包括本機快照集和雲端快照集。 |
| 可以在任何裝置中呈現的快照集數目上限 |10,000 | |
| 可以針對備份、還原或複製進行平行處理的磁碟區數目上限 |16 |<ul><li>如果有 16 個以上的磁碟區，當有可用的處理插槽時會依序處理它們。</li><li>作業完成前，無法對於備份或還原的分層式磁碟區進行新的備份。 不過，對於本機磁碟區，在磁碟區上線後允許備份。</li></ul> |
| 分層磁碟區的還原和複製復原時間 |< 2 分鐘 |<ul><li>無論磁碟區大小為何，磁碟區均可在 2 分鐘內用於還原或複製作業。</li><li>磁碟區效能一開始可能會比正常情況慢，因為大部分資料和中繼資料仍然位於雲端。 資料從雲端流向 StorSimple 裝置時，效能會提升。</li><li>下載中繼資料的總時間取決於配置的磁碟區大小。 中繼資料會於背景自動帶入裝置，速率為每 TB 的配置磁碟區資料 5 分鐘。 此速率可能會受到雲端的網際網路頻寬影響。</li><li>所有中繼資料均位於裝置時，還原或複製作業即已完成。</li><li>還原或複製作業全部完成前，無法執行備份作業。 |
| 固定在本機的磁碟區的還原復原時間 |< 2 分鐘 |<ul><li>無論磁碟區大小為何，磁碟區均可在 2 分鐘內用於還原或複製作業。</li><li>磁碟區效能一開始可能會比正常情況慢，因為大部分資料和中繼資料仍然位於雲端。 資料從雲端流向 StorSimple 裝置時，效能會提升。</li><li>下載中繼資料的總時間取決於配置的磁碟區大小。 中繼資料會於背景自動帶入裝置，速率為每 TB 的配置磁碟區資料 5 分鐘。 此速率可能會受到雲端的網際網路頻寬影響。</li><li>與分層磁碟區不同，就固定在本機的磁碟區而言，磁碟區資料會一併下載到本機裝置。 當所有磁碟區資料都已下載到裝置時，還原作業即已完成。</li><li>還原作業的時間可能會很久，且完成還原的總時間將取決於所佈建的本機磁碟區大小、網際網路頻寬及裝置上現有的資料。 在進行還原作業時，允許在固定在本機的磁碟區上進行備份作業。 |
| 精簡還原可用性 |前次容錯移轉 | |
| 用戶端讀取/寫入輸送量最大值 (從 SSD 層提供服務時)* |920/720 MB/秒，使用單一 10GbE 網路介面 |最多 2x，使用 MPIO 和兩個網路介面。 |
| 用戶端讀取/寫入輸送量最大值 (從 HDD 層提供服務時)* |120/250 MB/秒 | |
| 用戶端讀取/寫入輸送量最大值 (從雲端層提供服務時)* |11/41 MB/秒 |讀取輸送量取決於用戶端產生和維護足夠的 I/O 佇列深度。 |

&#42; 每個 I/O 類型的輸送量最大值的測量方式是使用 100% 讀取和 100% 寫入案例。 實際輸送量可能較低，取決於 I/O 混合和網路狀況。

