# Needleman−Wunsch algorithm in Swift

__Update__: I've finally used this implementation in Swift as the first step to create a very simple iOS app to test how the Needleman−Wunsch algorithm works. More details can be found [in this repo](https://github.com/almata/NeedlemanWunsch).

```swift
import Foundation

func needlemanWunsch(input1: String, input2: String, match: Int = 5, substitution: Int = -2, gap: Int = -6)
    -> (output1: String, output2: String, score: Int) {
        
    enum Origin { case top, left, diagonal }
    
    let seq1 = Array(input1) // Horizontal, so its length sets number of columns (j)
    let seq2 = Array(input2) // Vertical, so its length sets number of rows (i)
    
    var scores: [[Int]] = []
    var paths: [[[Origin]]] = []
    
    // Initialize both matrixes with zeros.
    for _ in 0...seq2.count {
        scores.append(Array(repeatElement(0, count: seq1.count + 1)))
        paths.append(Array(repeatElement([], count: seq1.count + 1)))
    }
    
    // Initialize first rows and columns.
    for j in 1...seq1.count {
        scores[0][j] = scores[0][j - 1] + gap
        paths[0][j] = [.left]
    }
    for i in 1...seq2.count {
        scores[i][0] = scores[i - 1][0] + gap
        paths[i][0] = [.top]
    }
    
    // Populate the rest of both matrices.
    for i in 1...seq2.count {
        for j in 1...seq1.count {
            let fromTop = scores[i - 1][j] + gap
            let fromLeft = scores[i][j - 1] + gap
            let fromDiagonal = scores[i - 1][j - 1] + (seq1[j - 1] == seq2[i - 1] ? match : substitution)
            let fromMax = max(fromTop, fromLeft, fromDiagonal)
            
            scores[i][j] = fromMax
            
            if fromDiagonal == fromMax { paths[i][j].append(.diagonal) }
            if fromTop == fromMax { paths[i][j].append(.top) }
            if fromLeft == fromMax { paths[i][j].append(.left) }
        }
    }
    
    // Get the alignment representation.
    var output1 = ""
    var output2 = ""
    
    var i = seq2.count
    var j = seq1.count
    
    while !(i == 0 && j == 0) {
        switch paths[i][j].first! {
        case .diagonal:
            output1 = String(seq1[j - 1]) + output1
            output2 = String(seq2[i - 1]) + output2
            i -= 1
            j -= 1
        case .top:
            output1 = "-" + output1
            output2 = String(seq2[i - 1]) + output2
            i -= 1
        case .left:
            output1 = String(seq1[j - 1]) + output1
            output2 = "-" + output2
            j -= 1
        }
    }

    return (output1, output2, scores[seq2.count][seq1.count])
}
```

```swift
let alignment = needlemanWunsch(input1: "GATTACA", input2: "GGTACAA")
print(alignment.output1) // GATTAC-A
print(alignment.output2) // G-GTACAA
print(alignment.score)   // 11
```
