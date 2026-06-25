---
share: "true"
---
Compose Icon组件

```kotlin
package com.tospery.myandroid.samples  
  
import androidx.annotation.DrawableRes  
import androidx.compose.foundation.background  
import androidx.compose.foundation.border  
import androidx.compose.foundation.clickable  
import androidx.compose.foundation.layout.Arrangement  
import androidx.compose.foundation.layout.Column  
import androidx.compose.foundation.layout.Row  
import androidx.compose.foundation.layout.fillMaxSize  
import androidx.compose.foundation.layout.fillMaxWidth  
import androidx.compose.foundation.layout.padding  
import androidx.compose.foundation.layout.size  
import androidx.compose.foundation.lazy.LazyColumn  
import androidx.compose.foundation.shape.CircleShape  
import androidx.compose.foundation.shape.RoundedCornerShape  
import androidx.compose.material.icons.Icons  
import androidx.compose.material.icons.filled.Add  
import androidx.compose.material.icons.filled.CheckCircle  
import androidx.compose.material.icons.filled.Home  
import androidx.compose.material.icons.filled.Person  
import androidx.compose.material.icons.filled.Search  
import androidx.compose.material3.AssistChip  
import androidx.compose.material3.ElevatedCard  
import androidx.compose.material3.FilledIconButton  
import androidx.compose.material3.Icon  
import androidx.compose.material3.IconButton  
import androidx.compose.material3.MaterialTheme  
import androidx.compose.material3.Text  
import androidx.compose.runtime.Composable  
import androidx.compose.runtime.getValue  
import androidx.compose.runtime.mutableStateOf  
import androidx.compose.runtime.remember  
import androidx.compose.runtime.setValue  
import androidx.compose.ui.Alignment  
import androidx.compose.ui.Modifier  
import androidx.compose.ui.draw.alpha  
import androidx.compose.ui.draw.clip  
import androidx.compose.ui.graphics.Color  
import androidx.compose.ui.graphics.vector.ImageVector  
import androidx.compose.ui.res.painterResource  
import androidx.compose.ui.unit.dp  
import com.tospery.myandroid.LocalSampleLogger  
import com.tospery.myandroid.R  
import com.tospery.myandroid.Sample  
  
val ComposeIconSample = Sample(  
    title = "Compose Icon组件",  
    sourcePath = "ComposeIconSample.kt",  
    content = { ComposeIconSampleContent() }  
)  
  
@Composable  
private fun ComposeIconSampleContent() {  
    val onLog = LocalSampleLogger.current  
    LazyColumn(  
        modifier = Modifier.fillMaxSize(),  
        verticalArrangement = Arrangement.spacedBy(16.dp)  
    ) {  
        item { VectorIconCard(onLog = onLog) }  
        item { LocalPainterIconCard(onLog = onLog) }  
        item { InteractiveIconCard(onLog = onLog) }  
        item { IconStyleCard(onLog = onLog) }  
        item { AccessibilityIconCard(onLog = onLog) }  
    }  
}  
  
@Composable  
private fun VectorIconCard(  
    onLog: (String) -> Unit  
) {  
    ElevatedCard(  
        modifier = Modifier.fillMaxWidth()  
    ) {  
        Column(  
            modifier = Modifier.padding(20.dp),  
            verticalArrangement = Arrangement.spacedBy(14.dp)  
        ) {  
            Text(  
                text = "Material 图标",  
                style = MaterialTheme.typography.titleLarge  
            )  
            Text(  
                text = "Icon 可以直接使用 Material Icons 提供的 ImageVector。",  
                style = MaterialTheme.typography.bodyMedium,  
                color = MaterialTheme.colorScheme.onSurfaceVariant  
            )  
            Row(  
                modifier = Modifier.fillMaxWidth(),  
                horizontalArrangement = Arrangement.SpaceEvenly  
            ) {  
                VectorIconTile(  
                    label = "Home",  
                    imageVector = Icons.Default.Home,  
                    onClick = {  
                        onLog("Compose Icon组件: 点击 Material 图标 Home")  
                    }  
                )  
                VectorIconTile(  
                    label = "Search",  
                    imageVector = Icons.Default.Search,  
                    onClick = {  
                        onLog("Compose Icon组件: 点击 Material 图标 Search")  
                    }  
                )  
                VectorIconTile(  
                    label = "Person",  
                    imageVector = Icons.Default.Person,  
                    onClick = {  
                        onLog("Compose Icon组件: 点击 Material 图标 Person")  
                    }  
                )  
            }  
        }  
    }  
}  
  
@Composable  
private fun VectorIconTile(  
    label: String,  
    imageVector: ImageVector,  
    onClick: () -> Unit  
) {  
    Column(  
        modifier = Modifier  
            // Icon 本身不负责点击，交互能力通常由 Modifier.clickable 或 IconButton 提供。  
            .clickable(onClick = onClick)  
            .padding(6.dp),  
        verticalArrangement = Arrangement.spacedBy(8.dp),  
        horizontalAlignment = Alignment.CenterHorizontally  
    ) {  
        Icon(  
            imageVector = imageVector,  
            contentDescription = label,  
            modifier = Modifier.size(36.dp),  
            tint = MaterialTheme.colorScheme.primary  
        )  
        Text(  
            text = label,  
            style = MaterialTheme.typography.labelLarge  
        )  
    }  
}  
  
@Composable  
private fun LocalPainterIconCard(  
    onLog: (String) -> Unit  
) {  
    ElevatedCard(  
        modifier = Modifier.fillMaxWidth()  
    ) {  
        Column(  
            modifier = Modifier.padding(20.dp),  
            verticalArrangement = Arrangement.spacedBy(14.dp)  
        ) {  
            Text(  
                text = "本地图标资源",  
                style = MaterialTheme.typography.titleLarge  
            )  
            Text(  
                text = "PNG 图标可以通过 painterResource 加载，再交给 Icon 显示。",  
                style = MaterialTheme.typography.bodyMedium,  
                color = MaterialTheme.colorScheme.onSurfaceVariant  
            )  
            Row(  
                modifier = Modifier.fillMaxWidth(),  
                horizontalArrangement = Arrangement.SpaceEvenly  
            ) {  
                PainterIconTile(  
                    label = "首页",  
                    iconResId = R.drawable.icon_home,  
                    onClick = {  
                        onLog("Compose Icon组件: 点击本地图标 首页")  
                    }  
                )  
                PainterIconTile(  
                    label = "用户",  
                    iconResId = R.drawable.icon_user,  
                    onClick = {  
                        onLog("Compose Icon组件: 点击本地图标 用户")  
                    }  
                )  
                PainterIconTile(  
                    label = "添加",  
                    iconResId = R.drawable.icon_add,  
                    onClick = {  
                        onLog("Compose Icon组件: 点击本地图标 添加")  
                    }  
                )  
            }  
        }  
    }  
}  
  
@Composable  
private fun PainterIconTile(  
    label: String,  
    @DrawableRes iconResId: Int,  
    onClick: () -> Unit  
) {  
    Column(  
        modifier = Modifier  
            .clickable(onClick = onClick)  
            .padding(6.dp),  
        verticalArrangement = Arrangement.spacedBy(8.dp),  
        horizontalAlignment = Alignment.CenterHorizontally  
    ) {  
        Icon(  
            painter = painterResource(id = iconResId),  
            contentDescription = label,  
            modifier = Modifier.size(36.dp),  
            // 对多色 PNG 使用 Color.Unspecified，保留原始像素颜色。  
            tint = Color.Unspecified  
        )  
        Text(  
            text = label,  
            style = MaterialTheme.typography.labelLarge  
        )  
    }  
}  
  
@Composable  
private fun InteractiveIconCard(  
    onLog: (String) -> Unit  
) {  
    var collected by remember { mutableStateOf(false) }  
    ElevatedCard(  
        modifier = Modifier.fillMaxWidth()  
    ) {  
        Column(  
            modifier = Modifier.padding(20.dp),  
            verticalArrangement = Arrangement.spacedBy(14.dp)  
        ) {  
            Text(  
                text = "IconButton 交互",  
                style = MaterialTheme.typography.titleLarge  
            )  
            Text(  
                text = "Icon 常与 IconButton 组合，作为可点击操作的视觉标识。",  
                style = MaterialTheme.typography.bodyMedium,  
                color = MaterialTheme.colorScheme.onSurfaceVariant  
            )  
            Row(  
                verticalAlignment = Alignment.CenterVertically,  
                horizontalArrangement = Arrangement.spacedBy(16.dp)  
            ) {  
                IconButton(  
                    onClick = {  
                        collected = !collected  
                        onLog("Compose Icon组件: ${if (collected) "收藏" else "取消收藏"}")  
                    }  
                ) {  
                    // IconButton 提供 Material 触摸目标、涟漪和可访问性动作语义。  
                    Icon(  
                        painter = painterResource(  
                            id = if (collected) {  
                                R.drawable.icon_collect_selected  
                            } else {  
                                R.drawable.icon_collect_normal  
                            }  
                        ),  
                        // 交互图标的描述要随状态变化，否则读屏用户会听到错误动作。  
                        contentDescription = if (collected) "取消收藏" else "收藏",  
                        tint = Color.Unspecified  
                    )  
                }  
                FilledIconButton(  
                    onClick = {  
                        onLog("Compose Icon组件: 点击添加")  
                    }  
                ) {  
                    Icon(  
                        imageVector = Icons.Default.Add,  
                        contentDescription = "添加"  
                    )  
                }  
            }  
        }  
    }  
}  
  
@Composable  
private fun IconStyleCard(  
    onLog: (String) -> Unit  
) {  
    ElevatedCard(  
        modifier = Modifier.fillMaxWidth()  
    ) {  
        Column(  
            modifier = Modifier.padding(20.dp),  
            verticalArrangement = Arrangement.spacedBy(14.dp)  
        ) {  
            Text(  
                text = "尺寸、颜色和状态",  
                style = MaterialTheme.typography.titleLarge  
            )  
            Text(  
                text = "Icon 的尺寸由 Modifier 控制，颜色通常使用 MaterialTheme 的 colorScheme。",  
                style = MaterialTheme.typography.bodyMedium,  
                color = MaterialTheme.colorScheme.onSurfaceVariant  
            )  
            Row(  
                modifier = Modifier.fillMaxWidth(),  
                horizontalArrangement = Arrangement.SpaceEvenly,  
                verticalAlignment = Alignment.CenterVertically  
            ) {  
                Icon(  
                    imageVector = Icons.Default.CheckCircle,  
                    contentDescription = "完成",  
                    modifier = Modifier  
                        .size(28.dp)  
                        .clickable {  
                            onLog("Compose Icon组件: 点击 28dp 基础图标")  
                        },  
                    tint = MaterialTheme.colorScheme.primary  
                )  
                Icon(  
                    imageVector = Icons.Default.CheckCircle,  
                    contentDescription = "强调完成",  
                    modifier = Modifier  
                        .size(48.dp)  
                        .clickable {  
                            onLog("Compose Icon组件: 点击带背景的强调图标")  
                        }  
                        .clip(CircleShape)  
                        .background(MaterialTheme.colorScheme.primaryContainer)  
                        .padding(10.dp),  
                    tint = MaterialTheme.colorScheme.onPrimaryContainer  
                )  
                Icon(  
                    imageVector = Icons.Default.CheckCircle,  
                    contentDescription = "不可用",  
                    modifier = Modifier  
                        .size(36.dp)  
                        .clickable {  
                            onLog("Compose Icon组件: 点击不可用视觉状态图标")  
                        }  
                        .alpha(0.38f),  
                    tint = MaterialTheme.colorScheme.onSurface  
                )  
            }  
        }  
    }  
}  
  
@Composable  
private fun AccessibilityIconCard(  
    onLog: (String) -> Unit  
) {  
    ElevatedCard(  
        modifier = Modifier.fillMaxWidth()  
    ) {  
        Column(  
            modifier = Modifier.padding(20.dp),  
            verticalArrangement = Arrangement.spacedBy(12.dp)  
        ) {  
            Text(  
                text = "可访问性建议",  
                style = MaterialTheme.typography.titleLarge  
            )  
            Text(  
                text = "装饰性图标使用 null；表达操作或状态的图标提供明确描述。",  
                style = MaterialTheme.typography.bodyMedium,  
                color = MaterialTheme.colorScheme.onSurfaceVariant  
            )  
            Row(  
                horizontalArrangement = Arrangement.spacedBy(10.dp),  
                verticalAlignment = Alignment.CenterVertically  
            ) {  
                AssistChip(  
                    onClick = {  
                        onLog("Compose Icon组件: 点击装饰图标说明")  
                    },  
                    label = { Text(text = "装饰图标") },  
                    leadingIcon = {  
                        // 文本已经说明含义时，leadingIcon 通常只是装饰。  
                        Icon(  
                            imageVector = Icons.Default.CheckCircle,  
                            contentDescription = null  
                        )  
                    }  
                )  
                Icon(  
                    imageVector = Icons.Default.Search,  
                    contentDescription = "搜索",  
                    modifier = Modifier  
                        .size(42.dp)  
                        .clickable {  
                            onLog("Compose Icon组件: 点击有语义描述的搜索图标")  
                        }  
                        .clip(RoundedCornerShape(12.dp))  
                        .border(  
                            width = 1.dp,  
                            color = MaterialTheme.colorScheme.outlineVariant,  
                            shape = RoundedCornerShape(12.dp)  
                        )  
                        .padding(9.dp),  
                    tint = MaterialTheme.colorScheme.secondary  
                )  
            }  
        }  
    }  
}
```


