public async Task<List<ProductDto>> GetProductsAsync(string category = null)
{
    return await _dbContext.Products
        .Where(p => category == null || p.Category == category)
        .Select(p => new ProductDto { Id = p.Id, Name = p.Name, Category = p.Category })
        .ToListAsync();
}