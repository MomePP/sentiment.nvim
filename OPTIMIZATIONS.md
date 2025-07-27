# Sentiment.nvim Performance Optimizations

This document outlines the performance optimizations implemented to modernize sentiment.nvim using current Neovim APIs while maintaining backward compatibility and addressing fast event context requirements.

## Summary of Optimizations

### 1. Modern Highlighting API
- **Before**: `vim.api.nvim_buf_add_highlight()`
- **After**: `vim.api.nvim_buf_set_extmark()` 
- **Benefits**: Modern API usage, better consistency with Neovim's direction
- **Note**: Ephemeral highlighting was removed as it requires decoration provider callbacks

### 2. Improved Timer System
- **Implementation**: Retained `vim.defer_fn()` for fast event compatibility
- **Benefits**: Reliable execution in all event contexts, prevents E5560 errors
- **Note**: `vim.uv.new_timer()` was tested but caused fast event context issues

### 3. Cached Namespace IDs
- **Before**: Creating namespace on every render call
- **After**: Cached namespace ID with single creation
- **Benefits**: Reduced API calls, better performance

### 4. Modern Mode Detection
- **Before**: `vim.fn.mode()`
- **After**: `vim.api.nvim_get_mode().mode`
- **Benefits**: Direct API access, better performance

### 5. Modern Buffer Option Access
- **Before**: `vim.bo[buf].buftype` and `vim.bo[buf].filetype`
- **After**: `vim.api.nvim_get_option_value()` with buffer context
- **Benefits**: Direct API access, explicit buffer targeting

### 6. Modern Window Information
- **Before**: Multiple `vim.fn.line("w0", win)` and `vim.fn.line("w$", win)` calls
- **After**: Single `vim.fn.getwininfo(win)[1]` call
- **Benefits**: Single API call instead of multiple, better performance

### 7. Enhanced Event Filtering
- **Implementation**: Mode-specific early exit for TextChanged events
- **Benefits**: Reduced unnecessary processing when events fire inappropriately

### 8. Early Validation
- **Implementation**: Validation before timer creation and after delay
- **Benefits**: Prevents unnecessary timer creation and processing for invalid states

### 9. Optimized Text Processing
- **Implementation**: Cached bounds and character-level optimizations
- **Benefits**: Fewer method calls during iteration, early exits for non-pair characters

## Performance Impact

The optimizations provide several key improvements:

1. **Reduced API Calls**: Cached namespace IDs and single window info calls
2. **Modern API Usage**: Updated to use current Neovim APIs while maintaining compatibility
3. **Safe Event Handling**: Proper fast event context management prevents runtime errors
4. **Early Validation**: Prevents unnecessary work in invalid states
5. **Enhanced Filtering**: Mode-specific processing reduces overhead

## Fast Event Context Considerations

Special attention was paid to Neovim's fast event context requirements:
- Timer system uses `vim.defer_fn()` for reliable execution in all contexts
- API calls are validated to work safely with fast events
- Ephemeral highlighting was removed due to decoration provider requirements

## Compatibility

All optimizations maintain full backward compatibility with the existing API:
- All public functions remain unchanged
- Configuration options remain the same
- Highlight groups and behavior remain identical
- User commands and setup process unchanged

## Testing

The optimizations have been validated through:
- Fast event context compatibility testing
- API usage verification against Neovim requirements  
- Performance benchmarking tests
- Edge case validation
- Backward compatibility verification

## Implementation Notes

Key lessons learned during implementation:
- Ephemeral extmarks require decoration provider callbacks and cannot be used directly
- Fast event contexts restrict certain API usage patterns
- Modern APIs provide better performance while maintaining compatibility
- Proper validation prevents runtime errors in edge cases

These changes bring sentiment.nvim up to modern Neovim standards while maintaining full compatibility with existing configurations and safe operation in all event contexts.