# String Pattern Match

给定一段文本 string = s0s1... ...sn-1
给定一个模式 pattern = p0p1... ...pm-1

求pattern 在string中出现的位置

T(n + m)

The 'lps' array is the key. Instead of starting from the begining each time, we start from the position which is the previous longest prefix suffix + 1.

```C++
void computeLPSArray(const std::string& pattern, std::vector<int>& lps) {
    int length = 0; // length of the previous longest prefix suffix
    lps[0] = 0; // lps[0] is always 0

    // the loop calculates lps[i] for i = 1 to pattern.length() - 1
    for (int i = 1; i < pattern.length(); i++) {
        while (length > 0 && pattern[i] != pattern[length]) {
            length = lps[length - 1];
        }
        if (pattern[i] == pattern[length]) {
            length++;
        }
        lps[i] = length;
    }
}


void KMPSearch(const std::string& text, const std::string& pattern) {
    int M = pattern.length();
    int N = text.length();

    // Create lps[] that will hold the longest prefix suffix values for the pattern
    std::vector<int> lps(M);

    // Preprocess the pattern (calculate lps[] array)
    computeLPSArray(pattern, lps);

    int j = 0; // index for pattern[]
    for (int i = 0; i < N; i++) {
        while (j > 0 && text[i] != pattern[j]) {
            j = lps[j - 1];
        }
        if (text[i] == pattern[j]) {
            j++;
        }
        if (j == M) {
            std::cout << "Found pattern at index " << i - j + 1 << std::endl;
            j = lps[j - 1];
        }
    }
}

```