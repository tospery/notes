---
share: "true"
---
Compose Text组件

```kotlin
package com.tospery.myandroid.samples  
  
import androidx.compose.foundation.layout.Arrangement  
import androidx.compose.foundation.layout.Column  
import androidx.compose.foundation.layout.fillMaxWidth  
import androidx.compose.foundation.layout.padding  
import androidx.compose.material3.ElevatedCard  
import androidx.compose.material3.MaterialTheme  
import androidx.compose.material3.Text  
import androidx.compose.runtime.Composable  
import androidx.compose.ui.Modifier  
import androidx.compose.ui.text.LinkAnnotation  
import androidx.compose.ui.text.SpanStyle  
import androidx.compose.ui.text.TextLinkStyles  
import androidx.compose.ui.text.buildAnnotatedString  
import androidx.compose.ui.text.font.FontWeight  
import androidx.compose.ui.text.style.TextAlign  
import androidx.compose.ui.text.style.TextOverflow  
import androidx.compose.ui.text.withLink  
import androidx.compose.ui.text.withStyle  
import androidx.compose.ui.unit.dp  
import com.tospery.myandroid.LocalSampleLogger  
import com.tospery.myandroid.Sample  
  
private const val ColorAnnotationTag = "color"  
  
val ComposeTextSample = Sample(  
    title = "Compose Text组件",  
    sourcePath = "ComposeTextSample.kt",  
    content = { ComposeTextSampleContent() }  
)  
  
@Composable  
private fun ComposeTextSampleContent() {  
    Column(  
        verticalArrangement = Arrangement.spacedBy(16.dp)  
    ) {  
        val onLog = LocalSampleLogger.current  
        ElevatedCard(  
            modifier = Modifier.fillMaxWidth()  
        ) {  
            Column(  
                modifier = Modifier.padding(20.dp),  
                verticalArrangement = Arrangement.spacedBy(10.dp)  
            ) {  
                Text(  
                    text = "Compose Text",  
                    style = MaterialTheme.typography.headlineMedium  
                )  
                Text(  
                    text = "使用 Material 主题排版展示标题、正文和辅助信息。",  
                    style = MaterialTheme.typography.bodyLarge,  
                    color = MaterialTheme.colorScheme.onSurfaceVariant  
                )  
                Text(  
                    text = "这段内容限制为单行，超出可用宽度后会使用省略号展示文本的溢出效果。",  
                    maxLines = 1,  
                    overflow = TextOverflow.Ellipsis,  
                    style = MaterialTheme.typography.bodyMedium  
                )  
            }  
        }  
        ElevatedCard(  
            modifier = Modifier.fillMaxWidth()  
        ) {  
            Column(  
                modifier = Modifier.padding(20.dp),  
                verticalArrangement = Arrangement.spacedBy(12.dp)  
            ) {  
                Text(  
                    text = "富文本样式",  
                    style = MaterialTheme.typography.titleMedium  
                )  
                val colorLink = LinkAnnotation.Clickable(  
                    tag = ColorAnnotationTag,  
                    styles = TextLinkStyles(  
                        style = SpanStyle(  
                            color = MaterialTheme.colorScheme.primary,  
                            fontWeight = FontWeight.Bold  
                        )  
                    ),  
                    linkInteractionListener = {  
                        onLog("Compose Text组件: 点击颜色")  
                    }  
                )  
                val richText = buildAnnotatedString {  
                    append("Text 可以组合 ")  
                    withLink(colorLink) {  
                        append("颜色")  
                    }  
                    append("、")  
                    withStyle(SpanStyle(fontWeight = FontWeight.SemiBold)) {  
                        append("粗细")  
                    }  
                    append(" 和可点击内容。")  
                }  
                Text(  
                    text = richText,  
                    style = MaterialTheme.typography.bodyLarge.copy(  
                        color = MaterialTheme.colorScheme.onSurface  
                    )  
                )  
                Text(  
                    text = "右对齐辅助文本",  
                    modifier = Modifier.fillMaxWidth(),  
                    textAlign = TextAlign.End,  
                    style = MaterialTheme.typography.labelLarge,  
                    color = MaterialTheme.colorScheme.secondary  
                )  
            }  
        }  
    }  
}
```

