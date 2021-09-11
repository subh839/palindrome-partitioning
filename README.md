# palindrome-partitioning

  int memo[501][501];

    // Utility function to check whether a string s[i..j] is palindrome or not
    bool isPalindrome(int x, int y, string s){
        int i , j;
        for(i = x, j = y; i <= j; ++i, --j){
            if( s[i] != s[j] )
                return false;
        }
        return true;
    }
    
    int minPalinParts_recur(int i, int j, string& s){
        
        if( i >= j || isPalindrome(i, j, s) )
            return 0;
            
        int minParts = INT_MAX;
        
        for(int k = i; k <= j - 1; ++k){
            int partitions = minPalinParts_recur(i, k, s) + minPalinParts_recur(k+1, j, s) + 1;
            minParts = min( minParts, partitions );
        }
        
        return minParts;
    }
    
    int minPalinParts_memo(int i, int j, string& s){
        
        if( i >= j || isPalindrome(i, j, s) )
            return ( memo[i][j] = 0 );
            
        if( memo[i][j] != -1 )
            return memo[i][j];
            
        int minParts = INT_MAX;
        
        for(int k = i; k <= j - 1; ++k){
            
            if( memo[i][k] == -1 )
                memo[i][k] = minPalinParts_memo(i, k, s);
            if( memo[k+1][j] == -1 )
                memo[k+1][j] = minPalinParts_memo(k+1, j, s);

            int partitions = memo[i][k] + memo[k+1][j] + 1;
            minParts = min( minParts, partitions );
        }
        
        return ( memo[i][j] = minParts );
    }
    
    int minPalinParts_bottomUp(int n, string& s){
        
        // Each entry in below table dp[i][j] shows minimum partitions needed for string s[i..j]
        vector<vector<int>> dp( n, vector<int>(n, INT_MAX) );
        
        // Single and same character is a palindrome hence 0 partitions needed
        for(int i = 0; i < n; ++i)
            dp[i][i] = 0;
            
        for(int i = n-2; i >= 0; --i){
            for(int j = i + 1; j < n; ++j){
                if( isPalindrome(i, j, s) )
                    dp[i][j] = 0;
                else{
                    // Now perform the partitioning step as done in above recursive method
                    for(int k = i; k < j; ++k)
                        dp[i][j] = min( dp[i][j], dp[i][k] + dp[k+1][j] + 1 );
                }
            }
        }
        return dp[0][n-1];  
    }
    
    int palindromicPartition(string str)
    {
        // This question is an extension/variation of Matrix Chain Multiplication method
        if( str.size() == 0 )
            return 0;
        return minPalinParts_bottomUp(str.size(), str);
    }
};
