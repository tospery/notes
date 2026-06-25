---
share: "true"
---
Compose状态恢复

```kotlin
package com.tospery.myandroid.samples  
  
import androidx.compose.foundation.layout.Arrangement  
import androidx.compose.foundation.layout.Column  
import androidx.compose.foundation.layout.Row  
import androidx.compose.foundation.layout.fillMaxSize  
import androidx.compose.foundation.layout.fillMaxWidth  
import androidx.compose.foundation.layout.padding  
import androidx.compose.foundation.lazy.LazyColumn  
import androidx.compose.material3.Button  
import androidx.compose.material3.ElevatedCard  
import androidx.compose.material3.FilledTonalButton  
import androidx.compose.material3.MaterialTheme  
import androidx.compose.material3.OutlinedButton  
import androidx.compose.material3.OutlinedTextField  
import androidx.compose.material3.Switch  
import androidx.compose.material3.Text  
import androidx.compose.runtime.Composable  
import androidx.compose.runtime.getValue  
import androidx.compose.runtime.mutableStateOf  
import androidx.compose.runtime.remember  
import androidx.compose.runtime.saveable.rememberSaveable  
import androidx.compose.runtime.saveable.rememberSaveableStateHolder  
import androidx.compose.runtime.setValue  
import androidx.compose.ui.Alignment  
import androidx.compose.ui.Modifier  
import androidx.compose.ui.unit.dp  
import com.tospery.myandroid.LocalSampleLogger  
import com.tospery.myandroid.Sample  
  
/**  
 * Compose 状态恢复（State Restoration）示例。  
 *  
 * 演示要点：  
 * 1. remember 只保存重组间状态，配置变更后会重新初始化。  
 * 2. rememberSaveable 会把可保存状态写入 SaveableStateRegistry。  
 * 3. 简单表单字段适合直接使用 rememberSaveable。  
 * 4. SaveableStateHolder 适合为被移出 Composition 的页面内容保留状态。  
 */  
val ComposeStateRestoreSample = Sample(  
    title = "Compose 状态恢复",  
    sourcePath = "ComposeStateRestoreSample.kt",  
    content = { ComposeStateRestoreSampleContent() }  
)  
  
@Composable  
private fun ComposeStateRestoreSampleContent() {  
    val onLog = LocalSampleLogger.current  
    LazyColumn(  
        modifier = Modifier.fillMaxSize(),  
        verticalArrangement = Arrangement.spacedBy(16.dp)  
    ) {  
        item { RememberSaveableCompareCard(onLog = onLog) }  
        item { SaveableFormCard(onLog = onLog) }  
        item { SaveableStateHolderCard(onLog = onLog) }  
    }  
}  
  
@Composable  
private fun RememberSaveableCompareCard(  
    onLog: (String) -> Unit  
) {  
    var rememberCount by remember { mutableStateOf(0) }  
    var saveableCount by rememberSaveable { mutableStateOf(0) }  
    SampleCard(  
        title = "remember vs rememberSaveable",  
        desc = "点击后旋转屏幕观察：remember 会丢失，rememberSaveable 会恢复。"  
    ) {  
        Text(  
            text = "remember: $rememberCount",  
            style = MaterialTheme.typography.titleMedium  
        )  
        Text(  
            text = "rememberSaveable: $saveableCount",  
            style = MaterialTheme.typography.titleMedium  
        )  
        Row(  
            horizontalArrangement = Arrangement.spacedBy(10.dp),  
            verticalAlignment = Alignment.CenterVertically  
        ) {  
            Button(  
                onClick = {  
                    rememberCount += 1  
                    saveableCount += 1  
                    onLog(  
                        "Compose 状态恢复: remember=$rememberCount saveable=$saveableCount"  
                    )  
                }  
            ) {  
                Text(text = "两者都 +1")  
            }  
            OutlinedButton(  
                onClick = {  
                    rememberCount = 0  
                    saveableCount = 0  
                    onLog("Compose 状态恢复: 计数已重置")  
                }  
            ) {  
                Text(text = "重置")  
            }  
        }  
    }  
}  
  
@Composable  
private fun SaveableFormCard(  
    onLog: (String) -> Unit  
) {  
    var nickname by rememberSaveable { mutableStateOf("") }  
    var minutes by rememberSaveable { mutableStateOf("25") }  
    var reminderEnabled by rememberSaveable { mutableStateOf(true) }  
    SampleCard(  
        title = "保存可恢复的表单状态",  
        desc = "String、Boolean、Int 等简单值可以直接保存到 Bundle，适合草稿类 UI 状态。"  
    ) {  
        OutlinedTextField(  
            value = nickname,  
            onValueChange = { input ->  
                nickname = input.take(12)  
                onLog("Compose 状态恢复: nickname = $nickname")  
            },  
            modifier = Modifier.fillMaxWidth(),  
            label = { Text(text = "昵称") },  
            singleLine = true  
        )  
        OutlinedTextField(  
            value = minutes,  
            onValueChange = { input ->  
                minutes = input.filter(Char::isDigit).take(3)  
                onLog("Compose 状态恢复: minutes = $minutes")  
            },  
            modifier = Modifier.fillMaxWidth(),  
            label = { Text(text = "学习分钟数") },  
            singleLine = true  
        )  
        Row(  
            modifier = Modifier.fillMaxWidth(),  
            horizontalArrangement = Arrangement.SpaceBetween,  
            verticalAlignment = Alignment.CenterVertically  
        ) {  
            Text(  
                text = "恢复后继续提醒",  
                style = MaterialTheme.typography.bodyLarge  
            )  
            Switch(  
                checked = reminderEnabled,  
                onCheckedChange = { checked ->  
                    reminderEnabled = checked  
                    onLog("Compose 状态恢复: reminderEnabled = $reminderEnabled")  
                }  
            )  
        }  
        Row(  
            horizontalArrangement = Arrangement.spacedBy(10.dp),  
            verticalAlignment = Alignment.CenterVertically  
        ) {  
            FilledTonalButton(  
                onClick = {  
                    nickname = "Compose"  
                    minutes = "45"  
                    reminderEnabled = true  
                    onLog("Compose 状态恢复: 已填入示例草稿")  
                }  
            ) {  
                Text(text = "填入示例")  
            }  
            OutlinedButton(  
                onClick = {  
                    nickname = ""  
                    minutes = "25"  
                    reminderEnabled = false  
                    onLog("Compose 状态恢复: 草稿已清空")  
                }  
            ) {  
                Text(text = "清空")  
            }  
        }  
    }  
}  
  
@Composable  
private fun SaveableStateHolderCard(  
    onLog: (String) -> Unit  
) {  
    var activePage by rememberSaveable { mutableStateOf("profile") }  
    val stateHolder = rememberSaveableStateHolder()  
    SampleCard(  
        title = "SaveableStateHolder",  
        desc = "切换页面会让另一页离开 Composition；StateHolder 会按 key 保存并恢复每页状态。"  
    ) {  
        Row(  
            horizontalArrangement = Arrangement.spacedBy(10.dp),  
            verticalAlignment = Alignment.CenterVertically  
        ) {  
            Button(  
                onClick = {  
                    activePage = "profile"  
                    onLog("Compose 状态恢复: 切换到资料页")  
                },  
                enabled = activePage != "profile"  
            ) {  
                Text(text = "资料页")  
            }  
            Button(  
                onClick = {  
                    activePage = "settings"  
                    onLog("Compose 状态恢复: 切换到设置页")  
                },  
                enabled = activePage != "settings"  
            ) {  
                Text(text = "设置页")  
            }  
        }  
        stateHolder.SaveableStateProvider(activePage) {  
            when (activePage) {  
                "profile" -> ProfileDraftPage(onLog = onLog)  
                else -> SettingsDraftPage(onLog = onLog)  
            }  
        }  
    }  
}  
  
@Composable  
private fun ProfileDraftPage(  
    onLog: (String) -> Unit  
) {  
    var username by rememberSaveable { mutableStateOf("") }  
    Column(  
        verticalArrangement = Arrangement.spacedBy(10.dp)  
    ) {  
        Text(  
            text = "资料页草稿",  
            style = MaterialTheme.typography.titleMedium  
        )  
        OutlinedTextField(  
            value = username,  
            onValueChange = { input ->  
                username = input.take(16)  
                onLog("Compose 状态恢复: profile username = $username")  
            },  
            modifier = Modifier.fillMaxWidth(),  
            label = { Text(text = "用户名") },  
            singleLine = true  
        )  
    }  
}  
  
@Composable  
private fun SettingsDraftPage(  
    onLog: (String) -> Unit  
) {  
    var darkMode by rememberSaveable { mutableStateOf(false) }  
    var compactMode by rememberSaveable { mutableStateOf(true) }  
    Column(  
        verticalArrangement = Arrangement.spacedBy(10.dp)  
    ) {  
        Text(  
            text = "设置页草稿",  
            style = MaterialTheme.typography.titleMedium  
        )  
        SettingRow(  
            title = "深色模式",  
            checked = darkMode,  
            onCheckedChange = { checked ->  
                darkMode = checked  
                onLog("Compose 状态恢复: darkMode = $darkMode")  
            }  
        )  
        SettingRow(  
            title = "紧凑布局",  
            checked = compactMode,  
            onCheckedChange = { checked ->  
                compactMode = checked  
                onLog("Compose 状态恢复: compactMode = $compactMode")  
            }  
        )  
    }  
}  
  
@Composable  
private fun SettingRow(  
    title: String,  
    checked: Boolean,  
    onCheckedChange: (Boolean) -> Unit  
) {  
    Row(  
        modifier = Modifier.fillMaxWidth(),  
        horizontalArrangement = Arrangement.SpaceBetween,  
        verticalAlignment = Alignment.CenterVertically  
    ) {  
        Text(  
            text = title,  
            style = MaterialTheme.typography.bodyLarge  
        )  
        Switch(  
            checked = checked,  
            onCheckedChange = onCheckedChange  
        )  
    }  
}  
  
@Composable  
private fun SampleCard(  
    title: String,  
    desc: String,  
    content: @Composable () -> Unit  
) {  
    ElevatedCard(modifier = Modifier.fillMaxWidth()) {  
        Column(  
            modifier = Modifier.padding(20.dp),  
            verticalArrangement = Arrangement.spacedBy(14.dp)  
        ) {  
            Text(  
                text = title,  
                style = MaterialTheme.typography.titleLarge  
            )  
            Text(  
                text = desc,  
                style = MaterialTheme.typography.bodyMedium,  
                color = MaterialTheme.colorScheme.onSurfaceVariant  
            )  
            content()  
        }  
    }  
}
```

